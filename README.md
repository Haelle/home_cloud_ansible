# raspberry_cloud_server_ansible
Ansible scripts to deploy a cloud server (Plex, Nextcloud, backups etc...)

# Installation

1. Enable SSH on the Raspberry
2. Set the IP of you Raspberry in `inventory/raspberry`
3. Create a new secrets files `ansible-vault create <my-secrets.yml>` with the following vars:
  - pi_password (use `mkpasswd --method=sha-512` to generate the hash)
  - pi_ssh_key (remove it from `media-server.yml` if you don't want an ssh-key)
  - nextcloud_admin_password
  - nextcloud_db_password
  - certbot_email

Reference this file in `media-server.yml` instead of `./secrets.yml`

3. Run some ansible commands
```bash
ansible-galaxy install -r requirements.yml
ansible-playbook media-server.yml
```

# Other

See all ansible variables for a host
```bash
ansible u- pi -m setup <hostname>
```

## Check piplening is enabled
If in the log there is only ONE `SSH: EXEC ssh....` it's working if not there will be 3-5 occurences (https://stackoverflow.com/questions/43438519/check-if-ansible-pipelining-is-enabled-working) :
```bash
ansible raspberry -vvv -u pi -m shell -a 'echo ok'
```

# TODO:
backup ?
