---
title: Import Grafana Dashboards
description: Jacker - import Grafana dashboards
authors:
    - Javier Cañete Arroyo
date: 2024-03-11
---

# Import Grafana Dashboards

Let's import some Grafana Dashboards to help us monitoring our system.

## Step 1: Create Datasource

First of all we will create a datasource to connect Grafana with our Prometheus instance that is already collecting data from the services included in Jacker stack:

Open a web browser and point it to: https://grafana.mybox.example.com

![Screenshot](/assets/img/01_grafana_config.jpeg){ .center-image }

Now in the top left menu navigate to "Connections":

![Screenshot](/assets/img/02_grafana_config.jpeg){ .center-image }

Navigate through all available Data sources and click on `Prometheus`:

![Screenshot](/assets/img/03_grafana_config.jpeg){ .center-image }

Click on `Add new data source` in the top right corner:

![Screenshot](/assets/img/04_grafana_config.jpeg){ .center-image }

Insert Prometheus server URL, scroll down and click on `Save and test`:

!!! tip "Prometheus server URL"
    ``` bash
    http://prometheus:9090
    ```

Now we can see our newly created data source to feed our Dashboards, click `Data sources` in the left pane:

![Screenshot](/assets/img/05_grafana_config.jpeg){ .center-image }

## Step 2: Import Dashboards

Now let's import Node Exporter Dashboard so we can keep an eye on what's happening in our global host. If you do a Google search `grafana node exporter` you will easily find this resource:

!!! tip "Grafana Node Exporter Dashboard"
    ``` bash
    https://github.com/rfmoz/grafana-dashboards/blob/master/prometheus/node-exporter-full.json
    ```

Just copy all the contents and go back to your Grafana, in the top menu click `Dashboards` and then in the top right click `New > Import`.

Paste all the contents in the `Ìmport via dashboard JSON model` box and click `Load`.

Select Prometheus Datasource and click `Import`. Voilà !!

![Screenshot](/assets/img/06_grafana_config.jpeg){ .center-image }


## Step 3: More Dashboards

Repeat Step 2 and import these dashboards:

- Traefik
    - https://github.com/tcheronneau/grafana_dashboard/blob/master/traefik.json
- Crowdsec
    - https://github.com/crowdsecurity/grafana-dashboards/tree/master/dashboards_v5
- Prometheus
    - https://github.com/tcheronneau/grafana_dashboard/blob/master/prometheus-system.json

