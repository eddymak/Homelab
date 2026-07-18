# What to do when you buy a 2nd-hand server motherboard but don't know the management port IP address?
I bought a 2nd-hand ASRock D1521D4I to run Proxmox. Since I don't have a VGA adapter, the only way to access the console (and boot from the Proxmox installer ISO USB) is through the IPMI remote KVM. Unfortunately, the seller did't provide the configured IPMI IP address. 
Instead of running a network scan, which would take forever since I didn't even know the subnet, connect the port to your computer/local network, one of the following will happen:
 - The port is configured for DHCP. It will request an IP address from the network after plugging it in. Find the IP address by checking the DHCP lease history.
 - The port is configured with a static IP. It will (hopefully) broadcast ARP requests to look for the gateway. The configured IP address will be shown in the ARP request information in a packet capture. We can then guess the subnet mask and try to connect.  *<- This was my case* <img width="1681" height="132" alt="image" src="https://github.com/user-attachments/assets/d3ff851f-f481-4b9c-904b-1a1a3945ca85" />

