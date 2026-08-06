# Physical Interfaces
  There are 4 physical ethernet ports (igc0-3) on the Protectli VP2420, OPNsense is installed on it and acting as a layer 3 gateway for all networks.
  | Port | Name | Purpose |
  |----------|---------|---------|
  | igc0 | WAN | Connected to the ISP ONT for internet|
  | igc1 | LAN (Trunk) | Connected to the MikroTik CRS310 switch as an 802.1Q trunk carrying all VLANs |
  | igc2 | - | Not used |
  | igc3 | MGMT_OOB | A dedicated management port for firewall management in case of break glass scenario |

# VLANS and VLAN interfaces
  The network is segmented into multiple VLANs to improve security and isolate devices with different trust levels.

  OPNsense performs all inter-VLAN routing, ensuring that communication between networks is only permitted through explicitly defined firewall rules. This ensures all inter-VLAN traffic are inspected, also provides a centralized traffic management.
  
| VLAN | Purpose |
|------|---------|
| WiFi Users | Personal wireless devices such as laptops and phones |
| WiFi IoT | Smart home devices with restricted network access |
| LAN Servers | Proxmox virtual machines and self-hosted services |
| Management | Management network for network devices and the Proxmox host |
| *(Planned)* LAN Users | Wired desktop devices |
| *(Planned)* DMZ | Public-facing services isolated from the internal network |

  Each VLAN is configured in OPNsense as a virtual interface and acts as the default gateway for devices within that subnet. To configure VLANs on OPNsense:
  1. Navigate to Interfaces -> Devices -> VLAN and define the VLAN. Select the parent interface and assign a VLAN tag
       <img width="847" height="281" alt="image" src="https://github.com/user-attachments/assets/52c8e920-ab90-4af6-ad9f-d91b963aaa18" />
  2. Navigate to Interfaces -> Assignments. Add the VLAN interfaces.
       <img width="591" height="166" alt="image" src="https://github.com/user-attachments/assets/b924fc2a-8f22-458e-93e1-9a5f4733178d" />
  3. Navigate to Interfaces -> [Interface name]. Enable the VLAN interface and configure an IP address
       <img width="531" height="156" alt="image" src="https://github.com/user-attachments/assets/4f0be026-1c01-461e-a006-2f0337340dcf" />
       <img width="559" height="123" alt="image" src="https://github.com/user-attachments/assets/17edf407-f872-4a55-bf07-5cbfc7b1c3dd" />
  

# Firewall rules
# NAT rules
  Outbound NAT is performed by OPNsense. Internal networks are translated to the WAN interface address to provide internet access.
  *Manual outbound NAT rule generation* is selected as I want complete control of my NAT rules.  
  <img width="853" height="522" alt="image" src="https://github.com/user-attachments/assets/9680c487-3313-4856-a0c8-a94478e8f8dc" />

# WireGuard VPN
# DHCP
# Dynamic DNS
# Security
