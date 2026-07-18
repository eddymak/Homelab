# Welcome to my Homelab!
This is my personal playground for networking, cybersecurity, and automation. The goal is to build a secure and resilient home network architecture for my self-hosted services and home automation, while providing network and security monitoring capabilities, and most importantly, learning and having fun!

This repo documents the network design, hardware, services, and configurations used in my homelab setup. It is still a work in progress. Feel free to explore and open an issue if you have any questions or suggestions for improvement.

# Network Diagram
<div align="center">
  <img width="600" height="600" alt="image" src="https://github.com/user-attachments/assets/14abb6fd-4545-4a1e-81c0-63863ab92206" />
</div>


The network is simple, but a nice diagram is always helpful when explaining things. 

A single Protectli appliance runs OPNsense as the edge firewall/router. The WAN port connects directly to the ONT box, and the LAN port connects to the HP Instant On AP. 

The Temp port is being used temporarily to connect my Proxmox hypervisor, I will merge the 2 ports together once my MikroTik switch arrives. 

Both the LAN and Temp are configured as trunk ports, this is required because the AP serves two SSIDs mapped to different VLANs, and the AP and Proxmox management networks are on separate VLAN as well.

The Proxmox hypervisor runs a single Linux Bridge (vmbr), and VLAN tags are assigned directly to the individual VMs.

# Hardware

## Networking
| Role          | Model         | OS            |    Notes         | 
| ------------- | ------------- | ------------- | -------------    |
| Edge firewall | Protectli Vault Pro VP2420    | OPNsense         | Intel Celeron J6412, 8GB DDR4 RAM, 128GB M.2 SATA, 4x 2.5 Gb ports |
| WiFi Access Point             | HPE (Aruba) Instant On AP21      | - | 2 SSIDs for users and IoT devices |

## Servers
| Role          | Model         | OS            |    Notes         | 
| ------------- | ------------- | ------------- | -------------    |
| VM  Host      | ASRock D1521D4I               | Proxmox VE       | Xeon D1521, 16GB DDR4 RAM, 2TB M.2 NVMe, 2x Gb ports |

# Services
# Security
# Automation

# Troubleshooting
This section documents the technical issues encountered during the development of this homelab.

- [What to do when you buy a 2nd-hand device but you don't know the management port IP address?](Troubleshooting/What%20to%20do%20when%20you%20buy%20a%202nd-hand%20device%20but%20you%20don't%20know%20the%20management%20port%20IP%20address%3F.md)
