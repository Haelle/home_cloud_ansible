# raspberry_cloud_server_ansible
Ansible scripts to deploy a cloud server (Plex, Nextcloud, RAID, backups etc...)

# Installation

1. Set the IP of you Raspberry in `inventory/raspberry`

2. Create a new secrets files `ansible-vault create <my-secrets.yml>` with the following vars:
  - pi_password (use `mkpasswd --method=sha-512` to generate the hash)
  - pi_ssh_key (remove it from `media-server.yml` if you don't want an ssh-key)
  - nextcloud_admin_password
  - nextcloud_db_password

Reference this file in `media-server.yml` instead of `./secrets.yml`

3. Run some ansible commands
```bash
ansible-galaxy install -r requirements.yml
ansible-playbook media-server.yml
```

# TODO:
NFS disk
when RAID 1 change NFS conf
Plex :
to add
sudo chown -h -R plex:plex Plex\ Media\ Server
sudo chown -h -R plex:plex path_to_real_dir
forget ntfs... need ext4 for Plex & nfs !!!

----------------
To do by hand
enable UFW ansible fails to enable on strech ?!
$ sudo ufw enable
