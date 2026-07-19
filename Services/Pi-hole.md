# Pi-hole
Pi-hole is an open-source DNS forwarder that filters DNS queries using community-maintained blocklists. It blocks requests to ads, tracking, malware, and phishing domains or other possibly harmful domains at the DNS level.
Visit this [github repo](https://github.com/hagezi/dns-blocklists) or other places to get subscribed lists. 

Typical DNS flow with Pi-hole would be:
<div align="center">
  <img width="720" height="251" alt="image" src="https://github.com/user-attachments/assets/91aed8bb-b958-4587-8e29-779142440570" />
</div>

In this environment, Pi-hole is deployed as a Docker container on an Ubuntu server. To ensure all DNS queries pass through Pi-hole:
 - Assign Pi-hole as the clients DNS server via DHCP
 - Block all outbound DNS traffic except from Pi-hole on the edge firewall
 - Block DNS-over-HTTPS (DoH), DNS-over-TLS (DoT), and DNS-over-QUIC (DoQ) on the edge firewall.

# Installation
## Prerequisite
Install Docker and Docker Compose:
```
sudo apt update
sudo apt install docker.io
sudo apt install docker-compose
```
Ubuntu enables systemd-resolved by default. Disable the Ubuntu systemd-resolved to avoid port conflicts on TCP/UDP 53:
```
sudo systemctl disable --now systemd-resolved
```
Also update /etc/resolv.conf to use an external DNS server.

## Create the compose file
Copy the compose.yaml from the [official repo](https://github.com/pi-hole/docker-pi-hole/#quick-start) to a directory and modify as needed. In this environment, only the following lines were changed:
```
 - "8001:80/tcp" 
 - "4431:443/tcp"

FTLCONF_webserver_api_password: ${PIHOLE_PASSWORD}
```
The first two lines map different Ubuntu host ports to the Pi-hole web GUI to avoid port conflicts from other web services.
The third line loads the Pi-hole web interface password from a .env file. 
Create a .env file in the same directory as compose.yaml to store the password.

Run Docker Compose:
```
docker compose up -d
```

The Pi-hole web GUI should be accessible now.
<img width="1470" height="1149" alt="image" src="https://github.com/user-attachments/assets/fba2adaf-06ae-4d07-8d6c-f0a29fef9c42" />


# Configuration
# Security
