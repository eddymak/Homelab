# Physical Interfaces
  There are 4 physical Ethernet ports (igc0-3) on the Protectli VP2420, OPNsense is installed on it and acting as a layer 3 gateway for all networks.
  | Port | Name | Purpose |
  |----------|---------|---------|
  | igc0 | WAN | Connected to the ISP ONT for internet|
  | igc1 | LAN (Trunk) | Connected to the MikroTik CRS310 switch as an 802.1Q trunk carrying all VLANs |
  | igc2 | - | Not used |
  | igc3 | MGMT_OOB | A dedicated management port for firewall management in case of break glass scenario |

  All 3 physical Ethernet ports on the MiktoTik CRS310 belong to the same bridge and are configured as hybrid/trunk ports carrying multiple VLANs:
  
  <img width="725" height="417" alt="image" src="https://github.com/user-attachments/assets/67e757b1-06ed-4711-93d1-db17f29eee9a" />

  # VLANS, VLAN interfaces and Trunks
  The network is segmented into multiple VLANs to improve security and isolate devices with different trust levels.

  OPNsense performs all inter-VLAN routing, ensuring that communication between networks is only permitted through explicitly defined firewall rules. This ensures all inter-VLAN traffic are inspected, also provides a centralized traffic management.
  
| VLAN | Tag | Purpose |
|------| --- | ---------|
| WiFi Users | 105 | Personal wireless devices such as laptops and phones |
| WiFi IoT | 107 | Smart home devices with restricted network access |
| LAN Servers | 20 | Proxmox virtual machines and self-hosted services |
| Management | 99 | Management network for network devices and the Proxmox host |
| *(Planned)* LAN Users | - | Wired desktop devices |
| *(Planned)* DMZ | - | Public-facing services isolated from the internal network |

  Each VLAN is configured in OPNsense as a virtual interface and acts as the default gateway for devices within that subnet. To configure VLANs on OPNsense:
  1. Navigate to Interfaces -> Devices -> VLAN and define the VLAN. Select the parent interface and assign a VLAN tag.
       <img width="847" height="281" alt="image" src="https://github.com/user-attachments/assets/52c8e920-ab90-4af6-ad9f-d91b963aaa18" />
  2. Navigate to Interfaces -> Assignments. Add the VLAN interfaces.
       <img width="591" height="166" alt="image" src="https://github.com/user-attachments/assets/b924fc2a-8f22-458e-93e1-9a5f4733178d" />
  3. Navigate to Interfaces -> [Interface name]. Enable the VLAN interface and configure an IP address.
       <img width="531" height="156" alt="image" src="https://github.com/user-attachments/assets/4f0be026-1c01-461e-a006-2f0337340dcf" />
       <img width="559" height="123" alt="image" src="https://github.com/user-attachments/assets/17edf407-f872-4a55-bf07-5cbfc7b1c3dd" />

  On CRS310, *vlan-filtering* is enabled on the bridge. VLANs are created on the page Bridge -> VLANs:
    <img width="763" height="295" alt="image" src="https://github.com/user-attachments/assets/6911f7bc-ad38-4b5f-bce7-24a383833589" />
  
  VLAN99 is used as the MGMT VLAN. Not all devices support VLAN tagging out of the box, so native VLAN are used to assign untagged frames to MGMT VLAN. Native VLANs can be enabled on the page Bridge -> Ports using the PVID option:
    <img width="749" height="197" alt="image" src="https://github.com/user-attachments/assets/c96cbe38-f607-4988-a5d4-02193f4569a8" />
  
  And the untagged option on the page Bridge -> VLANs:
    <img width="599" height="456" alt="image" src="https://github.com/user-attachments/assets/d5c13a4a-01e9-4ba7-b154-abc32dbb0f7d" />


# Firewall rules

The firewall rules are designed according to the principle of least privilege. Rules are grouped into the categories below to improve readability and management.

| Category | Purpose |
| -------- | ------- |
| ICMP filtering | Blocks all ICMP packets except from trusted sources | 
| DNS filtering | Blocks all DNS traffic except to/from PiHole | 
| WireGuard VPN | Controls WireGuard connectivity, allowing traffic between remote site and local subnet |
| WAN inbound | Blocks inbound internet traffic for logging purpose | 
| Security | Blocks traffic to and from IP addresses listed in the Spamhaus DROP list | 
| Firewall management | Restricts access to the OPNsense administration interface to specific sources |
| Internet access | Provides internet access while preventing access to internal address ranges |

<img width="2903" height="1352" alt="image" src="https://github.com/user-attachments/assets/3dcad1d5-71ee-4723-84bd-3cedaf30469d" />

As the network grows, I will keep updating the firewall rules for a more granular traffic control. 

# NAT rules
  Outbound NAT is performed by OPNsense. Internal networks are translated to the WAN interface address to provide internet access.
  *Manual outbound NAT rule generation* is selected as I want complete control of my NAT rules.  
  <img width="853" height="522" alt="image" src="https://github.com/user-attachments/assets/9680c487-3313-4856-a0c8-a94478e8f8dc" />

# DHCP
  Kea DHCP service is enabled on the virtual interfaces on OPNsense. The 99, 105 and 107 subnets are defined with the below options:

  | Field | Value |
  | ---------- | ------------------ |
  |  Valid lifetime | 300s |
  | DNS server | Pi-hole IP address |
  | NTP server | Virtual interface IP address |
  | Gateway | Virtual interface IP address |

  Here, I changed the valid lifetime from the default 4000s to 300s. This allows clients to obtain updated DHCP settings more quickly after configuration changes. This wouldn't hurt the firewall performance as my network is still small. 

  DHCP reservations are also configured to ensure that specific devices (e.g. my PC, APs) always get the same predefined IP address based on their MAC addresses.

  <img width="817" height="310" alt="image" src="https://github.com/user-attachments/assets/0674cb20-ab6a-427d-8844-ce173dd3bdf3" />

# Dynamic DNS
  I am using Cloudflare as the authoritative DNS provider for my public domain. Dynamic DNS service is configured on OPNsense to allow automatic update of DNS record in Cloudflare as my WAN IP address is changed by ISP. 
  
  To configure Dynamic DNS:
  1. On Cloudflare portal, navigate to Account Profile (Upper right corner) -> API token. Add a API token with Edit Zone DNS permission.  
  1. On OPNsense, navigate to Firmware -> Plugins and install the os-ddclient plugin.
  2. Navigate to Services -> Dynamic DNS -> Accounts and setup with the below details:
     | Field | Value |
     | ------- | ---------- |
     | Service | Cloudflare |
     | Password | API token created on Cloudflare |
     | Zone | The DNS zone containing the dynamic DNS record | 
     | Hostname | The DNS record that requires dynamic update |
     | Check IP method | Interface [IPv4] |
     | Interface to monitor | WAN |
     
     <img width="964" height="330" alt="image" src="https://github.com/user-attachments/assets/d55e14dc-2543-4581-abc7-28a01f721c78" />

  
# Security
# WireGuard VPN


