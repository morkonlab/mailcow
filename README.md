# Mailcow with Borgmatic backups

## Prepare storage box
- Create subuser
- Create new SSH keys with ssh-keygen
- cat ~/.ssh/<keyfile> | ssh -p23 u******@u******.your-storagebox.de install-ssh-key
- Test: sftp -P <22 or 23> u******@u******.your-storagebox.de

## Mailcow

Install as root, umask = 0022

- curl -sSL https://get.docker.com/ | CHANNEL=stable sh
- systemctl enable --now docker
- cd /opt
- git clone https://github.com/mailcow/mailcow-dockerized
- cd mailcow-dockerized
- ./generate_config.sh
- nano docker-compose.override.yml
- mkdir -p data/conf/borgmatic/etc
- source mailcow.conf
- cat <\<EOF > data/conf/borgmatic/etc/config.yaml
- add to config: ssh_command: ssh -i /root/.ssh/<keyfile>
- mkdir data/conf/borgmatic/ssh
- place keyfile in data/conf/borgmatic/ssh
- chmod 600 data/conf/borgmatic/<keyfile>
- nano data/conf/borgmatic/etc/crontab.txt
- docker compose up -d
- docker compose exec borgmatic-mailcow borgmatic init --encryption repokey-blake2
- docker compose restart borgmatic-mailcow


### Create initial backup
```
docker compose exec borgmatic-mailcow borgmatic --stats -v 0
```

### View stats
```
docker compose exec borgmatic-mailcow borgmatic --stats
```

### List
```
docker compose exec borgmatic-mailcow borgmatic list
```

### Info
```
docker compose exec borgmatic-mailcow borgmatic info
```
