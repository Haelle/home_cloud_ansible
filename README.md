# home_cloud_ansible

Ansible scripts to deploy a cloud server (Plex, Nextcloud, backups etc...)

It needs Ansible community tasks:

```shell
ansible-galaxy collection install community.general
ansible-galaxy collection install community.docker
```

## Installation

1. Enable SSH on the Raspberry
2. Set the local IP of the Raspberry in `inventory/hosts`
3. Create a new secrets files `ansible-vault create <my-secrets.yml>` with the following vars:

- `pi_password` (use `mkpasswd --method=sha-512` to generate the hash)
- `pi_ssh_key` (remove it from `media-server.yml` if you don't want an ssh-key)
- `nextcloud_admin_password`
- `nextcloud_db_password`
- `certbot_email`

Reference this file in playbooks instead of `./secrets.yml`

3. Run some ansible commands

```bash
ansible-galaxy install -r requirements.yml
ansible-playbook rothomagus.yml
```

## Other

See all ansible variables for a host

```bash
ansible -u pi -m setup <hostname>
```

### Nextcloud sync problems

Nextcloud sometimes fails to sync, I can be because of differences appearing between Nextcloud database and the file system.

It can be fixed with this command :

```shell
sudo -u www-data php occ db:add-missing-indices
sudo -u www-data php occ files:scan --all
sudo -u www-data php occ files:cleanup
```

The second one is the most important, and tackle most of the problems

### Plex claiming problems

It Plex is on another server (especially inside de container) you may not be able to claim the server.
Plex does not see the server as on the same network. Instead of trying to fix the network configuration let's make a tunnel.

```shell
ssh ip.address.of.server -L 8888:localhost:32400
```

And then go to `http://localhost:8888/web`, and claim it.

P.S: make sure all other SSH connection are close, if not ssh *may* use them as shared connection and not creating the tunnel.

[Source](https://support.plex.tv/articles/200288666-opening-plex-web-app/)

### Check piplening is enabled

If in the log there is only ONE `SSH: EXEC ssh....` it's working if not there will be 3-5 occurences (https://stackoverflow.com/questions/43438519/check-if-ansible-pipelining-is-enabled-working) :

```bash
ansible raspberry -vvv -u pi -m shell -a 'echo ok'
```

## Firefox & KeepassXC

Actuellement KeepassXC n'arrive pas à communiquer avec Firefox natif (version snap).

Il faut donc :
- désinstaller firefox de snap `sudo snap remove firefox`
- ajouter le ppa de firefox  `sudo add-apt-repository ppa:mozillateam/ppa`
- rendre ce ppa prioritaire par rapport à snap
- installer firefox

```config
# /etc/apt/preferences.d/mozilla-firefox
Package: *
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001
```

## CopyQ & KeePassXC

CopyQ stock l'historique des c/p ; afin d'éviter qu'il stocke les mots de passe de KeePassXC il faut activer [une commande](https://github.com/hluk/copyq-commands/blob/master/Automatic/keepassxc-protector.ini)
