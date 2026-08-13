# Prometheus & Grafana

I use [Prometheus](https://github.com/prometheus/prometheus) and [Grafana](https://github.com/grafana/grafana) to monitor systems and services in my homelab. 

Prometheus acts as the central metrics backend, collecting and storing time-series data from systems and services. Grafana provides an interface to query and visualise the data through dashboards.

Currently monitoring:
  - OPNsense (Through [opnsense-exporter](https://github.com/AthennaMind/opnsense-exporter))

# Installation

## Prerequisite
Install Docker and Docker Compose:
```
sudo apt update
sudo apt install docker.io
sudo apt install docker-compose
```

## Create the compose file

Prometheus, Grafana, and opnsense-exporter are deployed using a single docker compose file. I modified the compose configuration from this [repo](https://github.com/docker/awesome-compose/blob/master/prometheus-grafana/README.md) and added the compose configuration form [opnsense-exporter](https://github.com/AthennaMind/opnsense-exporter#docker-compose) for the opnsense metrics.

File structure:

```
.
├── .env
├── compose.yaml
├── grafana
│   └── datasource.yml
└── prometheus
    └── prometheus.yml
```

Configuration files:

 - compose.yaml
 - datasource.yml: Define the Grafana data source
 - prometheus.yml: Define the Prometheus scrape targets

Using a single compose file allows docker compose to create a shared network for the containers. Containers on the same compose network can communicate with each other using their service names instead of IP addresses. For example, in the Grafana datasource.yml, Grafana can connect to Prometheus using the Prometheus service name in the URL:

```
datasources:
- name: Prometheus
  type: prometheus
  url: http://prometheus:9090

```

This makes the compose more portable. 

Run Docker Compose:
```
docker compose up -d
```

Verify the services are running and collecting metrics:
 - http://<IP_address>:8080/
   <img width="1806" height="1053" alt="image" src="https://github.com/user-attachments/assets/ed234893-5750-48fe-bf28-73312d4dcfa8" />

 - http://<IP_address>:9090/targets
   <img width="1880" height="469" alt="image" src="https://github.com/user-attachments/assets/a0bc976e-5d0c-412d-b68f-46a975619bab" />

 - http://<IP_address>:3000/connections/datasources
   <img width="1873" height="510" alt="image" src="https://github.com/user-attachments/assets/e830df40-cbb9-4da8-9877-423be3490580" />



# Configuration
