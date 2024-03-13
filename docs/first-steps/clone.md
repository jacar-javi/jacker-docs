---
title: Clone Jacker Repo
description: Jacker - tasks to clone Jacker Repo
authors:
    - Javier Cañete Arroyo
date: 2024-03-12
---

# Clone Jacker Repo

Before running Jacker stack you must acomplish some tasks to make it work. First we will start cloning Jacker repository in our home directory:

!!! tip "Clone Jacker Repository"
    ``` bash
    cd ~
    git clone https://github.com/jacar-javi/jacker.git jacker
    cd jacker
    ```

!!! info "Commands in this doc"
    Every command included in this guide is pretended to be executed in the jacker root path. If you reboot your system or logout from shell remember to be prepared again in this path:
    ```
    cd ~/jacker
    ```

Jacker base dir structure is as follows:

```
jacker
├── assets
│   ├── <stack assets, scripts>
├── compose
│   ├── <stack service files yaml>
├── data
│   ├── <services data folders>
├── logs
│   ├── <services log folders>
├── secrets
│   ├── <stack secret files>
├── templates
│   ├── <stack templates files>
├── docker-compose.yml
├── CODE_OF_CONDUCT.md
├── README.md
├── setup.sh
└── .env
```

## setup.sh

Helper script to setup for the first time. It executes assets scripts if you want to.

## assets

Helper scripts to tune up your system before running Jacker.

## .env

Jacker Main Environment File. You will have to edit this file and setup with your needs as explained in this guide.

## secrets

Files that store secrets used by Jacker Platform. You will have to edit this file as explained in this guide