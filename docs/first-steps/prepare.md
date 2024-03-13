---
title: Prepare your system
description: Jacker - tasks to prepare your system for jacker
authors:
    - Javier Cañete Arroyo
date: 2024-03-08
---

# Prepare your system

Before running Jacker stack you must acomplish some tasks to make it work.

## Step 1: Prepare your host to receive connections from the internet

I reccomend you to use any subdomain for platform management purposes. This is **not** the domain you are serving webpages, blogs or any internet service but an access to all the applications and tools we will use to manage our docker infrastructure.

### Step 1.1: Create DNS Records

First of all we will create two DNS records that point to our host. One of type A with our **WAN_IP** and a second one of type CNAME that will point all subdomains to our host.

This way we won't have to worry about adding new services to our docker stack and they will work automatically:

| Type  | Name                | Content           | 
| ----- | ------------------- | ----------------- |
| A     | mybox.example.com   | **WAN_IP**        |
| CNAME | *.mybox.example.com | mybox.example.com |

### Step 1.2: Configure UFW Firewall

Since your host is starting to receive requests via several public DNS entries, you should configure your local firewall (if you haven't done so). We will first reset previous state and configure to default deny incoming connections and allow outgoing. We will allow connections to http and https ports and from local networks (adjust for your needs):

!!! tip "Configure UFW Firewall"
    ``` bash
    sudo ufw reset
    sudo ufw default deny incoming
    sudo ufw default allow outgoing

    sudo ufw allow from 192.168.0.0/24  # Adjust to your local LAN

    sudo ufw allow ssh    # You can get rid of this line if you connect to your host from any local LAN specified before 
    sudo ufw allow http   # Traefik web endpoint
    sudo ufw allow https  # Traefik websecure endpoint
    sudo ufw enable
    ```

!!! warning "Be carefull!!"
    Adjust this configuration to your needs as you could lose your connection to your host.

## Step 2: Configure Google OAuth2 Service

All our OAuth protected services will be hosted under our newly created DNS records, for example: **https://traefik.mybox.example.com**. 

We will create a google project that will contain our Credentials and Web App Consent Screen and Credentials for OAuth client ID.

### Step 2.1: Create Google Project

- Navigate to the [Google Cloud Devleopers Console](https://console.developers.google.com/)

- Click on **Select a project**

- Click on **Create a new project**

![Screenshot](/assets/img/01_oauth_config.webp){ .center-image }

- Enter a name to identify the project, suck as "Traefik Authentication"

![Screenshot](/assets/img/02_oauth_config.webp){ .center-image }

- Click **Create**

### Step 2.2: Create Oauth Credentials

- Select our newly created project and under the Navigation menu select **Credentials**. Click on **Create Credentials > OAuth client ID**.

![Screenshot](/assets/img/03_oauth_config.webp){ .center-image }

### Step 2.3: Configure the Consent Screen

![Screenshot](/assets/img/04_oauth_config.webp){ .center-image }

- Choose a name for your app, such ass "Traefik Auth".
- Under the **Authorized domains** add your FQDN of your box (ex. **mybox.example.com**)

![Screenshot](/assets/img/05_oauth_config.webp){ .center-image }

- Click **Save**

### Step 2.4: Create the OAuth client ID

- Select **Web Application** type and enter a name for your web application, such as "Traefik".
- Add your **Authorized redirect URI** as **https://oauth.mybox.example.com/_oauth**.

![Screenshot](/assets/img/06_oauth_config.webp){ .center-image }

- Click **Save**

The credentials for our SSO for Traefik and Docker have been created! Copy and save the **client ID** and **client secret** in the file /secrets/traefik_forward_oauth in **Jacker** structure.

![Screenshot](/assets/img/07_oauth_config.webp){ .center-image }


!!! tip "Get random value to configure in secret"
    ``` bash
    openssl rand -hex 16
    nano secrets/traefik_forward_oauth
    ```

```
### EXAMPLE OF REQUIRED FILE TO ALLOW DOCKER SECRETS TO FUNCTION WITH GOOGLE OAUTH
### FOR FURTHER DETAILS SEE https://github.com/thomseddon/traefik-forward-auth

providers.google.client-id=<YOUR_GOOGLE_CLIENT_ID>
providers.google.client-secret=<YOUR_GOOGLE_CLIENT_SECRET>
secret=<RANDOM NUMBER GENERATED TO ENCRYPT COOKIES>
whitelist=email1@example.com,email2@example.com (Emails who will get granted access to all your SSO apps)
```

Save changes with ++ctrl+x++ ++enter++