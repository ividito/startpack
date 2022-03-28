# startpack

This is a basic setup of services for faster startup development. You can run it via [docker-compose](https://docs.docker.com/compose/) or [docker swarm](https://docs.docker.com/engine/reference/commandline/stack/).

Warning: This setup doesn't provide high level of security or any [high availability](https://www.digitalocean.com/community/tutorials/what-is-high-availability). You have to hire some skilled devops engineer (like me)) for close this gap after getting first round or sales.

Also you can check [Awesome Selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted) and [Free for Dev](https://free-for.dev/) for more options ;)

Time track:
- [Filipp Frizzy](https://github.com/Friz-zy/): 57h 45m for 14 days

## Available and planned open source components

### Platform
* [DONE] [Docker Compose](https://docs.docker.com/compose/)
* [Not tested yet] [Docker Swarm](https://docs.docker.com/engine/reference/commandline/stack/)
* [DONE] [Traefik](https://traefik.io) as web server with autodiscovery and [letsencrypt](https://letsencrypt.org) certs
* [DONE] [NFS](https://hub.docker.com/r/itsthenetwork/nfs-server-alpine/) for docker swarm volumes
* [DONE] [Portainer](https://www.portainer.io/) as admin panel for docker services
* [DONE] [Docker registry](https://docs.docker.com/registry/) for store your docker images
* [DONE] [Influxdb 2](https://www.influxdata.com/blog/influxdb-2-0-open-source-is-generally-available/) and [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) for monitoring services
* [DONE] [Grafana](https://grafana.com/) and [Victoriametrics](https://victoriametrics.com) for monitoring services

### Databases and Storage
* [DONE] [Mariadb](https://mariadb.org/) as SQL database
* [DONE] [Postgresql](https://www.postgresql.org/) as another popular SQL database
* [DONE] [SQL Adminer](https://www.adminer.org/) as admin panel for SQL databases
* [DONE] [Minio](https://minio.io/) as s3 storage

### Management
* [DONE] [Gitlab](https://about.gitlab.com/) as git hosting and devops platform
* [DONE] [Nextcloud](https://nextcloud.com/) as cloud storage with plugins for emal, task management, password storage, etc
* [DONE] [Tuleap](https://www.tuleap.org/) as management software
* [DONE] [Openproject](https://www.openproject.org/) as management software
* [DONE] [Vaultwarden](https://github.com/dani-garcia/vaultwarden/wiki) as password manager for business
* [Backlog] [Zentao](https://www.zentao.pm) as scrum management software
* [Backlog] [Taiga](https://www.taiga.io/) as kanban board based management software
* [Backlog] [Owncloud](https://owncloud.com/) as cloud storage

### Chat
* [DONE] [rocket](https://rocket.chat/)
* [Backlog] [Mattermost](https://mattermost.com/)
* [Backlog] [Twake](https://twake.app/) as alternative to Microsoft Teams
* [Backlog] [Wire](https://wire.com) as alternative to Microsoft Teams

### Backend as a service
* [DONE] [Nocodb](https://www.nocodb.com/) as airtable alternative
* [DONE] [Strapi](https://strapi.io/) as headless CMS
* [Backlog] [Appwrite](https://appwrite.io/) as firebase alternative

### CI & CD
* [DONE] [Gitlab Runner](https://about.gitlab.com/) should be placed on separate host

## Support

You can support this or any other of my projects
  - [![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/filipp_frizzy)
  - [donationalerts.com/r/filipp_frizzy](https://www.donationalerts.com/r/filipp_frizzy)
  - ETH 0xCD9fC1719b9E174E911f343CA2B391060F931ff7
  - BTC bc1q8fhsj24f5ncv3995zk9v3jhwwmscecc6w0tdw3

## Setup

All operations should be executed from root on target machine. You can use your laptop or some server.
You need a valid domain name pointed to this server for automatically setting up https with [traefik](https://traefik.io) and [letsencrypt](https://letsencrypt.org). However, you can [hack your hosts file](https://docs.rackspace.com/support/how-to/modify-your-hosts-file/) for working without https.

If you run services with `docker-compose`, all service will be located on your single server. With `docker stack` (swarm) mode, you can [add addition servers](https://docs.docker.com/engine/swarm/swarm-tutorial/add-nodes/) in the same local network (the same network important for nfs volumes mounting unfortunately).

#### 1) Install docker if it doesn't installed
(run scripts from the internet is a bad practice, but if you don't know how to install docker with package managers - it's acceptable)
```
curl -fsSL https://get.docker.com -o get-docker.sh
DRY_RUN=1 sh ./get-docker.sh
sh ./get-docker.sh
```

#### 2) Install docker-compose or setup docker swarm
Install docker-compose if you don't wanna use more complex docker swarm.
```
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

[Setup docker swarm](https://docs.docker.com/engine/reference/commandline/swarm_init/) if you choose using it.
```
docker swarm init --advertise-addr $(hostname -I | awk '{print $1}')
```

#### 3) Get this repo
```
git checkout https://github.com/tldr-devops/startpack.git --depth=1
cd startpack
```

#### 4) Fill necessary variables like domain name of your server, your email, passwords for basic auth and sql services.

Generate random passwords
```
echo -e "export NEXTCLOUD_SQL_PASSWORD=$(echo $RANDOM `date`|md5sum|base64|head -c 25)\n$(cat env.sh)" > env.sh
echo -e "export TULEAP_SQL_PASSWORD=$(echo $RANDOM `date`|md5sum|base64|head -c 25)\n$(cat env.sh)" > env.sh
echo -e "export STRAPI_SQL_PASSWORD=$(echo $RANDOM `date`|md5sum|base64|head -c 25)\n$(cat env.sh)" > env.sh
echo -e "export GITLAB_SQL_PASSWORD=$(echo $RANDOM `date`|md5sum|base64|head -c 25)\n$(cat env.sh)" > env.sh
echo -e "export OPENPROJECT_SQL_PASSWORD=$(echo $RANDOM `date`|md5sum|base64|head -c 25)\n$(cat env.sh)" > env.sh
echo -e "export NOCODB_SQL_PASSWORD=$(echo $RANDOM `date`|md5sum|base64|head -c 25)\n$(cat env.sh)" > env.sh
echo -e "export REGISTRY_PASSWORD=$(echo $RANDOM `date`|md5sum|base64|head -c 25)\n$(cat env.sh)" > env.sh
echo -e "export SQL_ROOT_PASSWORD=$(echo $RANDOM `date`|md5sum|base64|head -c 25)\n$(cat env.sh)" > env.sh
echo -e "export PASSWORD=$(echo $RANDOM `date`|md5sum|base64|head -c 25)\n$(cat env.sh)" > env.sh
```

You should change this lines with your email and dns name
```
echo -e "export EMAIL='Type your email here'\n$(cat env.sh)" > env.sh
echo -e "export DOMAIN='Type your domain here'\n$(cat env.sh)" > env.sh
```

This is your credentials, store it in your password manager ;)
```
cat env.sh
```

#### 5) Prepare environment
```
source env.sh
bash setup.sh
```

#### 6) Run your new services

After entering all commands below you'll able to login into your new services by addresses:
* https://portainer.${YOUR DOMAIN}
* https://registry.${YOUR DOMAIN} user `your $REGISTRY_USERNAME` password `your $REGISTRY_PASSWORD`
* https://influxdb.${YOUR DOMAIN} user `your $USERNAME` password `your $PASSWORD`
* https://grafana.${YOUR DOMAIN} user `your $USERNAME` password `your $PASSWORD`
* https://victoriametrics.${YOUR DOMAIN} user `your $USERNAME` password `your $PASSWORD`
* https://adminer.${YOUR DOMAIN} user `your $USERNAME` password `your $PASSWORD`
* https://minio.${YOUR DOMAIN} user `your $USERNAME` password `your $PASSWORD`
* https://gitlab.${YOUR DOMAIN} user `root` password `your $PASSWORD`
* https://nextcloud.${YOUR DOMAIN} user `your $USERNAME` password `your $PASSWORD`
* https://tuleap.${YOUR DOMAIN} user `admin` password `your $PASSWORD`
* https://openproject.${YOUR DOMAIN} user `admin` password `admin`
* https://rocketchat.${YOUR DOMAIN}
* https://vaultwarden.${YOUR DOMAIN}/admin password `your $PASSWORD`
* https://nocodb.${YOUR DOMAIN}
* https://strapi.${YOUR DOMAIN}

##### Docker Compose
```
docker-compose -f setup-compose.yml up -d
docker-compose -f monitoring.yml up -d
docker-compose -f databases.yml up -d
docker-compose -f registry.yml up -d
docker-compose -f minio.yml up -d
docker-compose -f vaultwarden.yml up -d
docker-compose -f tuleap.yml up -d
docker-compose -f nextcloud.yml up -d
```

After enabling portainer you should immediately go to portainer.{your domain} and set admin password
```
docker-compose -f portainer.yml up -d
```

After enabling gitlab you should immediately go to gitlab.{your domain}, login with user `root` and your `$PASSWORD`,
and disable new user registration in configs.
```
docker-compose -f gitlab.yml up -d
```

After enabling rocketchat you should immediately go to rocketchat.{your domain}/admin and set admin password
```
docker-compose -f rocketchat.yml up -d
```

After enabling openproject you should immediately go to openproject.{your domain},
login with `admin` user and `admin` password, change it and update settings on
openproject.{your domain}/admin/settings/general
```
docker-compose -f openproject.yml up -d
```

After enabling nocodb you should immediately go to nocodb.{your domain} and set admin password
```
docker-compose -f nocodb.yml up -d
```

After enabling strapi you should immediately go to strapi.{your domain}/admin and set admin password
```
docker-compose -f strapi.yml up -d
```


##### Docker Swarm
```
docker stack deploy --compose-file setup-swarm.yml
docker stack deploy --compose-file monitoring.yml
docker stack deploy --compose-file databases.yml
docker stack deploy --compose-file registry.yml
docker stack deploy --compose-file minio.yml
docker stack deploy --compose-file vaultwarden.yml
docker stack deploy --compose-file tuleap.yml
docker stack deploy --compose-file nextcloud.yml
```

After enabling portainer you should immediately go to portainer.{your domain} and set admin password
```
docker stack deploy --compose-file portainer.yml
```

After enabling gitlab you should immediately go to gitlab.{your domain}, login with user `root` and your `$PASSWORD`,
and disable new user registration in configs.
```
docker stack deploy --compose-file gitlab.yml
```

After enabling rocketchat you should immediately go to rocketchat.{your domain}/admin and set admin password
```
docker stack deploy --compose-file rocketchat.yml
```

After enabling openproject you should immediately go to openproject.{your domain},
login with `admin` user and `admin` password, change it and update settings on
openproject.{your domain}/admin/settings/general
```
docker stack deploy --compose-file openproject.yml
```

After enabling nocodb you should immediately go to nocodb.{your domain} and set admin password
```
docker stack deploy --compose-file nocodb.yml
```

After enabling strapi you should immediately go to strapi.{your domain}/admin and set admin password
```
docker stack deploy --compose-file strapi.yml
```

#### 7) Run gitlab-runner on separate machine with docker-compose
```
# Install docker
curl -fsSL https://get.docker.com -o get-docker.sh
DRY_RUN=1 sh ./get-docker.sh
sh ./get-docker.sh

# Install docker-compose
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# Get files
git checkout https://github.com/tldr-devops/startpack.git --depth=1
cd startpack

# Prepare environment
mkdir -p {builds,cache}
export DOMAIN="Your domain"
export HASHED_PASSWORD="HASHED_PASSWORD from step 4"
envsubst < configs/gitlab-runner.toml > ./config.toml

# Run runner in docker with docker-compose
docker-compose -f gitlab-runner.yml up -d

# Check runners logs
docker-compose -f gitlab-runner.yml logs -f
```

#### 8) Login into your docker registry on all docker hosts
```
docker login -u "Your REGISTRY_USERNAME from step 4" -p "Your REGISTRY_PASSWORD from step 4" registry."YOUR DOMAIN"
```

#### 9) You should configure backups of your server, at least $DATAPATH directory
