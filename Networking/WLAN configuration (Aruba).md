# Aruba Instant On AP21

<div align="center">
  <img width="282" height="376" alt="image" src="https://github.com/user-attachments/assets/da9853d8-9178-408b-bf0f-1861d264091d" />
</div>

The Aruba Instant On AP21 was selected to balance cost (~£80 per AP), performance and functionality.

Features:
- Wi-Fi 6 (802.11ax) support
- Dual-band 2.4 GHz and 5 GHz
- Centralised cloud management
- Multiple SSIDs and VLAN tagging

It misses some advanced features such as 802.11k/v/r roaming assistance, but it is good enough for a homelab environment.
This may change if I move to a property that requires multiple APs and roaming becomes more important.

# AP onboarding

Onboarding the AP to the Aruba Instant On cloud portal is straightforward. After plugging in the AP to the network and making sure that it can access the internet, simply go through the below steps on the portal:

1. Registering an account and create a site
2. Enter the AP serial number
3. Set the operating mode (AP mode or Router mode. AP mode is set for my environment)

The AP should appear in the management portal shortly after onboarding.

# Wireless Configuration

Currently, one AP21 provides wireless connectivity across both 2.4 GHz and 5 GHz bands.

Two SSIDs are configured:

| SSID | Band | VLAN | Authentication | Purpose | 
|---|---|---|---|---|
| Users | 2.4 GHz and 5 GHz | 105 | WPA-Enterprise | User devices | 
| IoT | 2.4 GHz | 107 | WPA-Personal | IoT devices | 

The Users SSID authenticates through [RADIUS/NPS](../../main/Services/Active%20Directory/Active%20Directory.md#nps---configuring-a-radius-client), while IoT devices use a pre-shared key.

# VLAN and Switch Configuration

The AP21 connects to a trunk port on the MikroTik CRS310.

| VLAN | Purpose | Traffic |
|---|---|---|
| 99 | Management | Native VLAN |
| 105 | Users | Tagged |
| 107 | IoT | Tagged |

VLAN 99 is configured as the native VLAN on the switch port such that untagged AP management traffic is automatically assigned to VLAN 99 without explicitly configuring a management VLAN on the AP.

# Radio Management

I used the WiFi Analyzer app to identify less busy wireless channels.
I also reduced the transmit power to minimise wireless interference with neighbouring networks.

# IoT Network Security

An access policy restricts intra-subnet communication within the IoT subnet, preventing IoT devices from communicating directly with one another, mimicking what a isolated VLAN does (at least for layer 3).

<img width="1095" height="627" alt="image" src="https://github.com/user-attachments/assets/25cc84e4-65fa-4fa9-beb3-6791eb3526bc" />

All IoT should go traffic through the firewall for centralised traffic inspection and security policy enforcement.
