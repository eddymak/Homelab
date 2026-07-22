# Welcome to my Homelab!
This is my personal playground for networking, cybersecurity, and automation. The goal is to build a secure and resilient home network architecture for my self-hosted services and home automation, while providing network and security monitoring capabilities, and most importantly, learning and having fun!

This repo documents the network design, hardware, services, and configurations used in my homelab setup. It is still a work in progress. Feel free to explore and open an issue if you have any questions or suggestions for improvement.

# Network Diagram
<div align="center">
  <img width="663" height="991" alt="image" src="https://github.com/user-attachments/assets/2f8ba9c8-7820-4204-a47c-3ede98f26285" />
</div>


The network is simple, but a nice diagram is always helpful when explaining things. 

A single Protectli appliance runs OPNsense as the edge firewall and router. The WAN interface connects directly to the ISP ONT, the LAN interface connects to the MikroTik CRS310.

The CRS310 operates as the Layer 2 switch. All three connected switch ports are configured as trunk ports. This is required because:

- The wireless access point broadcasts 2 SSIDs, each mapped to seperate VLANs.
- Proxmox virtual machines are segmented using VLAN20.
- The AP and Proxmox management interface is placed on a dedicated Management VLAN99.

# Hardware

## Networking
| Role          | Model         | OS            |    Notes         | 
| ------------- | ------------- | ------------- | -------------    |
| Edge Firewall | Protectli Vault Pro VP2420    | OPNsense         | Intel Celeron J6412, 8GB DDR4 RAM, 128GB M.2 SATA SSD, 4x 2.5 Gb ports |
| L2/L3 Switch     | MikroTik CRS310 | RouterOS    | 8x 2.5 Gb ports, 2x SFP+ ports |
| WiFi Access Point             | HPE (Aruba) Instant On AP21      | - | 2 SSIDs for users and IoT networks |


## Servers
| Role          | Model         | OS            |    Notes         | 
| ------------- | ------------- | ------------- | -------------    |
| VM  Host      | ASRock D1521D4I               | Proxmox VE       | Xeon D1521, 16GB DDR4 RAM, 2TB M.2 NVMe, 2x Gb ports |

# Services
This section lists the services/applications that are running in the environment.

 - [Pi-hole](Services/Pi-hole/Pi-hole.md)

# Security
# Automation

# Troubleshooting
This section documents the technical issues encountered during the development of this homelab.

- [What to do when you buy a 2nd-hand device but you don't know the management port IP address?](Troubleshooting/What%20to%20do%20when%20you%20buy%20a%202nd-hand%20device%20but%20you%20don't%20know%20the%20management%20port%20IP%20address%3F.md)
