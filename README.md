# Personal-Website

#### Table of Contents

1. [Description](#description)
2. [Setup - Getting my webste up and running](#setup)
    - [Overview](#overview)
    - [Getting Docker Setup](#getting-docker-setup)
    - [Getting the OS Ready](#getting-the-os-ready)
    - [Configuring Traefik](#configuring-traefik)
    - [Spinning It Up](#spinning-it-up)
3. [Check It Out](#check-it-out)

## Description

This is my website. It currently serves as an extention to my resume/cv and it also serves as a demonstration of some of my experience with industry leading DevOps tools. 

My website is served using Nginx and uses Traefik V2 as the reverse proxy to route internal traffic within my domain. This allows me to easily add more services behind my domain without having to expose more ports to the outside web. 

The frontend is derived from a Hugo theme where I stripped it away from the Hugo engine so that I only need to use Nginx to serve the static files. Then I editted it to my liking.

All of the services are containerized using Docker and ochestrated used Docker-Compose.

LetsEncypt is used for the SSL certificate. The .dev domain requires https.

Lastly, it's all hosted inside of Digital Ocean's smallest available droplet which is my reason for using Docker-Compose for it's reduced overhead compared to that required by k8s. In the future, however, as I add more services I will most liekly move over to k8s.

## Setup

### Overview

The Docker-Compose yaml config file sets everything up: the docker network, the containers, and all of the configuration flags needed for Traefik to work properly.

### Getting Docker Setup

But first you need to install and setup Docker on your host machine.

Digital Ocean's small droplet with Ubuntu 18.04 LTS has Docker already installed.

### Getting the OS Ready

Create a new functional account to host the server:

```
useradd -dmG Docker webserver
```
This is will create a new user that is part of the Docker Group and it will create a home directory for that account.


Then, disable root login by setting up SSH key authentication:

```
cat ~/.ssh/id_dsa.pub | ssh root@[your_server] "cat >> ~/.ssh authorized_keys"
```

Then edit the file /etc/ssh/sshd_config and add this flag:
```
PermitRootLogin without-password
```

Lastly, restart the ssh service:
```
service ssh restart
```

When you SSH back in, `su webserver` to switch over to 'webserver' user.

You can then clone this git repository into the home directory (~/) of the user you created above.

### Configuring Traefik

Go through the docker-compose.yml file and the Traefik dynamic_conf.yml file to replace all instances of `chrisshort.dev` with your domain.

Make a new directory called letsencrypt:
```
mkdir letsencrypt
```
and `cd` into that new dir to create a file called `acme.json`:
```
touch acme.json
```
and then make the file only readable and writable by it's owner which the functional account you made earlier:
```
sudo chmod 600 acme.json
```

Replace the `#######` in the traefik.yml with your own email.

Then head over to the dynamic_conf.yml and replace the `#######` with the hash of your own password for signing into the Traefik dashboard that is located at: `https://traefik.<your.domain>`

IMPORTANT: Make sure that you have already added an A DNS entry that maps your domain to your Digitial Ocean server IP through your domain name registrar. You'll also want to add a CNAME DNS entry for 'traefik' that maps to your domain so that you can have Traefik dashboard functionality.

### Spinning It Up

If you've done everything right you should be able to simply run `docker-compose up`


## Check It Out

Head on over to https://<your.domain> to check out your website.


And likewise, head on over to https://chrisshort.dev to check out my website.



