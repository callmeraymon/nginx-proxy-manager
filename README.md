<p align="center">
	<img src="https://nginxproxymanager.com/github.png">
	<br><br>
</p>

This project is to deploy nginx-proxy-manager on synology with docker-compose.


## Project Goal

I created this project to use docker-compose instead of the built in docker GUI


## Home network setup

I won't go in to too much detail here but here are the basics for someone new to this self-hosted world.

1. Your home router will have a Port Forwarding section somewhere. Log in and find it
2. Add port forwarding for port 80 and 443 to the server hosting this project
3. Configure your domain name details to point to your home, either with a static ip or a service like DuckDNS or [Amazon Route53](https://github.com/jc21/route53-ddns)
4. Use the Nginx Proxy Manager as your gateway to forward to your other web based services

## Quick Setup

1. Install Docker and Docker-Compose

- [Docker Install documentation](https://docs.docker.com/install/)
- [Docker-Compose Install documentation](https://docs.docker.com/compose/install/)

2. Create the following file structure within your docker root, typically, /volume1/docker/

- /volume1/docker/npm/
- /volume1/docker/npm/data
- /volume1/docker/npm/letsencrypt

3. create file /volume1/docker/npm/config.json


3. Create a docker-compose.yml file similar to this:

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

3. Bring up your stack by running

```bash
docker-compose up -d
```

4. Log in to the Admin UI

When your docker container is running, connect to it on port `81` for the admin interface.
Sometimes this can take a little bit because of the entropy of keys.

[http://127.0.0.1:81](http://127.0.0.1:81)

Default Admin User:
```
Email:    admin@example.com
Password: changeme
```

Immediately after logging in with this default user you will be asked to modify your details and change your password.



<!-- markdownlint-enable -->
<!-- prettier-ignore-end -->
