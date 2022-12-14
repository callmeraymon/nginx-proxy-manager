<p align="center">
	<b>NGINX on Synology</b>
	<br><br>
</p>

This project is to deploy nginx-proxy-manager with docker-compose on Synology.


## Project Goal

I created this project to use docker-compose instead of the built in docker GUI. I also wanted NGINX to have the ability to proxy other devices on my network. Which is achieved with a macvlan network bridge.


## Requirements

- Add port forwarding for port 80 and 443 to the server hosting this project on router
- Configure DNS for nginx proxy manager
- Ability to SSH into DSM

## Setup

00. Install Docker and Docker-Compose

- [Docker Install documentation](https://docs.docker.com/install/)
- [Docker-Compose Install documentation](https://docs.docker.com/compose/install/)

00. Find the default network device for your Synology NAS
```bash
ifconfig
```

00. Create macvlan network

```bash
sudo docker network create -d macvlan-br0 -o parent=eth0 --subnet=192.168.254.0/24 --gateway=192.168.254.1 --ip-range=192.168.254.198/26 npm_network
```


00. Create the following file structure within your docker root, typically, /volume1/docker/

- /volume1/docker/npm/
- /volume1/docker/npm/data
- /volume1/docker/npm/letsencrypt

00. create file /volume1/docker/npm/config.json
```json
{
  "database": {
    "engine": "knex-native",
    "knex": {
      "client": "sqlite3",
      "connection": {
        "filename": "/data/database.sqlite"
      }
    }
  }
}
```

00. Create a docker-compose.yml file similar to this:

```yml
version: "3.3"
services:
    web:
        image: jc21/nginx-proxy-manager
        container_name: npm
        environment:
            - PUID=1026
            - PGID=100
            - TZ=America/Los_Angeles
        volumes:
            - /volume1/docker/npm/config.json:/app/config/production.json
            - /volume1/docker/npm/data:/data
            - /volume1/docker/npm/letsencrypt:/etc/letsencrypt
        networks:
            default:
                ipv4_address: 192.168.254.222
        ports:
            - 80:80
            - 443:443
            - 81:81
        restart: always

networks:
  default:
    external:
      name: macvlan-br0
```

00. Bring up stack by running

```bash
docker-compose up -d
```

00. Log in to the Admin UI

When your docker container is running, connect to it on port `81` for the admin interface.
Sometimes this can take a little bit because of the entropy of keys.

[http://npm.daomin.example:81](http://npm.domain.example:81)

Default Admin User:
```
Email:    admin@example.com
Password: changeme
```

Immediately after logging in with this default user you will be asked to modify your details and change your password.



<!-- markdownlint-enable -->
<!-- prettier-ignore-end -->
