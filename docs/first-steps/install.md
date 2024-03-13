---
title: Install Jacker
description: Jacker - platform installation
authors:
    - Javier Cañete Arroyo
date: 2024-03-10
---

# Install Jacker

This page explains how to install and run Jacker.

## Step 1: Configure Jacker environmet 

All the parameters used to configure Jacker's containers stack are defined in an environment file which contains all the variables that you have to setup to fit your needs. Some of them are set with default values to help you.

!!! warning "Default environment values"
    Don't change any of the default values unless you know what you are doing and you really need to do it

Set acme.json permissions and edit .env file and folow the instructions to setup environment:

!!! tip "Edit .env file"
    ``` bash
    chmod 600 data/traefik/acme.json
    nano .env
    ```

### Step 1.1 Configure Local User and Paths

Parameter   | Default                | Description                   | Linux command
----------- | ---------------------- | ----------------------------- | ---------------------
`PUID`      | 1000                   | User ID                       | ```id -u $USER```
`PGID`      | 1000                   | Group ID                      | ```id -g $USER```
`TZ`        | Europe/Madrid          | Timezone                      | ```cat /etc/timezone```
`USERDIR`   | /home/user             | User Home Path                | ```eval echo ~$USER```
`DOCKERDIR` | /home/user/jacker      | Path where you cloned jacker  |
`DATADIR`   | /home/user/jacker/data | Data dir inside jacker        |

### Step 1.2: Configure Networking

Parameter             | Default                                              | Description                                 | Linux command
--------------------- | ---------------------------------------------------- | ------------------------------------------- | ---------------------
`HOSTNAME`            | mybox                                                | Hostname                                    | ```hostname```
`DOMAINNAME`          | example.com                                          | Domain Name                                 |
`PUBLIC_FQDN`         | mybox.example.com                                    | Host FQDN                                   |
`LOCAL_IPS`           | 127.0.0.1/32,10.0.0.0/8,192.168.0.0/16,172.16.0.0/12 | Local IP Ranges                             |
`UFW_ALLOW_FROM`      |                                                      | Local IPs where you connect host from       |
`SOCKET_PROXY_SUBNET` | 192.168.70.0/24                                      | IP range for docker socket-proxy container  |
`SOCKET_PROXY_IP`     | 192.168.70.254                                       | Static IP for docker socket-proxy container |
`TRAEFIK_PROXY_SUBNET`| 192.168.71.0/24                                      | IP range for docker traefik network         |
`TRAEFIK_PROXY_IP`    | 192.168.71.254                                       | Static IP for docker traefik container      |
`HOST_IS_VM`          | true                                                 | true for VM, false for unprivileged LXC     |

### Step 1.3: Configure Let's Encrypt

Parameter           | Default       | Description                    | Linux command
------------------- | ------------- | ------------------------------ | ---------------------
`LETSENCRYPT_EMAIL` |               | Your email to get certificates | 

### Step 1.4: Configure Crowdsec

Parameter                          | Default       | Description                 | Linux command
---------------------------------- | ------------- | --------------------------- | ---------------------
`CROWDSEC_API_PORT`                | 8888          | Crowdsec API listening port |
`CROWDSEC_TRAEFIK_BOUNCER_API_KEY` |               | We will get this later on   |

Save changes with ++ctrl+x++ ++enter++

## Step 2: Start platform for the first time

We will start the Jacker platform for the first time, but it still won't work at all, since we have still a couple of things to configure.

!!! tip "Start Jacker platform"
    ``` bash
    docker compose up -d
    ```
### Step 2.1: Get Crowdsec Traefik Bouncer API key

!!! tip "Get crowdsec traefik bouncer api key"
    ``` bash
    docker compose exec crowdsec cscli bouncers add traefik-bouncer
    ```

Copy this value and set it to the appropiate variable in .env file:

!!! tip "Edit .env file"
    ``` bash
    nano .env
    ```

Parameter                          | Description 
---------------------------------- | --------------------------------------------
`CROWDSEC_TRAEFIK_BOUNCER_API_KEY` | Copy here the value we got from last command

### Step 2.2: Restart Pllatform

!!! tip "Restart platform"
    ``` bash
    docker compose down
    docker compose up -d
    ```

### Step 2.3: Configure Crowdsec Whitelists

As every internal request will pass through Crowdsec Traefik Bouncer, we will have to create a whitelist so the system doesn'g get banned itself:

!!! tip "Create custom Crowdsec whitelist"
    ``` bash
    sudo nano data/crowdsec/config/parsers/s02-enrich/custom-whitelist.yml
    ```

```
name: crowdsecurity/whitelists
description: "Whitelist events from my ip addresses"
whitelist:
  reason: "my ip ranges"
  ip:
    - "127.0.0.1" # Local Host
  cidr:
    - "192.168.0.0/16" # Local IPs
    - "10.0.0.0/8"     # Local IPs
    - "172.16.0.0/12"  # Local IPs
```

Save changes with ++ctrl+x++ ++enter++

!!! tip "Restart Crowdsec"
    ``` bash
    docker compose restart crowdsec
    ```

## Step 3: Install Crowdsec Firewall Bouncer

!!! tip "Install Crowdsec Firewall Bouncer"
    ``` bash
    curl -s https://packagecloud.io/install/repositories/crowdsec/crowdsec/script.deb.sh | sudo bash
    sudo apt install crowdsec-firewall-bouncer-iptables
    docker compose exec crowdsec cscli bouncers add host-firewall-bouncer
    ```

Copy de Crowdsec API key to store in config.

### Step 3.1: Configure Crowdsec Firewall Bouncer

!!! tip "Configure Crowdsec Firewall Bouncer"
    ``` bash
    sudo nano /etc/crowdsec/bouncers/crowdsec-firewall-bouncer.yaml
    ```

![Configure Crowdsec Firewall Bouncer](/assets/img/01_crowdsec_firewall_bouncer_config.webp){ .center-image }

<figcaption>

Configure Crowdsec Firewall Bouncer

</figcaption>

## Step 4: Check Jacker is running

We finished setting up Jacker Platform and we should have all services up & running.

Open a web browser and use some tabs to point to:

- https://traefik.mybox.example.com 

![Screenshot](/assets/img/traefik.jpeg){ .center-image }

- https://portainer.mybox.example.com 

![Screenshot](/assets/img/portainer.jpeg){ .center-image }

- https://grafana.mybox.example.com

![Screenshot](/assets/img/grafana_node-exporter.jpeg){ .center-image }

- https://code.mybox.example.com

![Screenshot](/assets/img/code_server.png){ .center-image }
