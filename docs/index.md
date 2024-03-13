---
title: "Jacker Platform Documentation"
description: "Jacker = Jacar + Docker is a docker based platform aimed at hosting services on the Internet and designed specifically to manage Docker containers with a focus on security."
authors:
    - Javier Cañete Arroyo
date: 2024-03-08
---

# Welcome

Welcome to Project Jacker :heart:{ .heart }, a powerful Docker stack combining Traefik and CrowdSec for enhanced security in your production public sites hosting, and Google Oauth for SSO to your management and monitoring tools.

Jacker is also intended to be a solid foundation on which to host new stacks of Docker-based components, establishing a series of foundations, standards and good practices to guarantee the security and stability necessary for any production system.

## Jacker = Jacar + Docker

``` mermaid
graph TB
  P[Google];
  S[Let's Encrypt CA];
  A[HTTP Request];
  subgraph Host
  Q[UFW / IPTables];
  R[Crowdsec Firewall Bouncer];
    subgraph Docker
        B[Traefik Reverse Proxy];
        T[SSL Certificates Store];
        C[Traefik Middleware ForwardAuth];
        D[Crowdsec Traefik Bouncer];
        E[Prometheus];
        F[OAuth];
        G[Grafana];
        N[Portainer];
        U[CodeServer];
        O[Node Exporter];
        H[Crowdsec Local Api];
        subgraph Docker Volumes
            I[Logs];
            J[Crowdsec DB];
            K[Crowdsec Config];
        end
        L[App 1];
        M[App N];
    end
  end
  B <--> S;
  B <--> T;
  L -.- M;
  D --> |Request API| H;
  H --> I;
  H --> J;
  H --> K;
  E --> H;
  E --> B;
  E --> O;
  G --> E;
  A --> Q;
  Q --> B;
  B -->|HTTP Request| C;
  C --> D;
  B --> I;
  C --> L & M;
  C --> N;
  C --> U;
  C --> G;
  D --> | OK / KO | C;
  C --> | If KO, return 403 | B;
  C --> F;
  F --> | OK / KO | C;
  F --> P;
  R --> H;
  R --> Q;
```

Project Jacker brings together four robust components:


- **Traefik Reverse Proxy**: Traefik is a modern reverse proxy and load balancer that simplifies routing, SSL termination, and service discovery for your Docker containers. It seamlessly integrates with your stack, ensuring smooth traffic flow and secure communication.

- **CrowdSec**: CrowdSec is a community-driven security solution that analyzes attacks in real time. It provides a console where you can dive into detailed information about IPs, including their activity rate, danger score, and the types of attacks they’ve carried out. With CrowdSec, you’ll be able to automatically ban malicious IPs from accessing your container services, bolstering your defenses.

- **OAuth**: OAuth (Open Authorization) is a standard protocol for secure authorization. Google provides OAuth services for authentication. Single Sign-On (SSO): OAuth allows users to authenticate once and access multiple services without repeated logins.

- **Monitoring: Prometheus & Node Exporter & Grafana**: Essential monitoring stack to watch what's happening in your setup:

    - Prometheus is an open-source monitoring system that provides robust metrics collection, storage, and alerting capabilities. It’s widely used in the DevOps and cloud-native ecosystem. 
    - Node Exporter, also known as Prometheus Node Exporter, is an essential component in the Prometheus ecosystem. It serves as a bridge between your Linux hosts and Prometheus, providing a wealth of hardware- and kernel-related metrics. When you run the Node Exporter on a machine, it collects crucial system information such as CPU usage, memory utilization, network statistics, disk space, and system load.
    - Grafana is a versatile open-source analytics and interactive visualization web application. It serves as a powerful tool for creating charts, graphs, and alerts when connected to supported data sources. Whether you’re monitoring system metrics, visualizing time series data, or building custom dashboards, Grafana provides an intuitive interface that empowers users to explore and understand their data effectively.

## What's In The Box

All internal services protected by Google OAuth with Single Sign On:

![Screenshot](/assets/img/google_oauth_login.webp){ .center-image }


- https://traefik.mybox.example.com 

![Screenshot](/assets/img/traefik.jpeg){ .center-image }

- https://portainer.mybox.example.com 

![Screenshot](/assets/img/portainer.jpeg){ .center-image }

- https://grafana.mybox.example.com

![Screenshot](/assets/img/grafana_node-exporter.jpeg){ .center-image }

- https://code.mybox.example.com

![Screenshot](/assets/img/code_server.png){ .center-image }
