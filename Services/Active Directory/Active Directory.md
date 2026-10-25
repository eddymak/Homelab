# Active Directory

I use Active Directory as the directory service in my homelab.

The Windows Server hosts the following roles and components:

- Active Directory Domain Services (AD DS)
- Active Directory Certificate Services (AD CS) with Web Enrollment
- Network Policy Server (NPS)

<img width="581" height="359" alt="Installed Windows Server roles and features" src="https://github.com/user-attachments/assets/869714a3-c0a1-4dfc-82f9-419b92876d9a" />

The server also acts as an authoritative DNS server for my internal domain. Pi-hole uses [conditional forwarding](../Pi-hole/Pi-hole.md#conditional-forwarding) to forward DNS queries for the internal domain to the AD DNS server.

The Web Enrollment component of AD CS is optional. I installed it to provide a web-based interface for certificate requests, making certificate enrollment more convenient.

# Configuration

The initial AD DS and AD CS configurations are relatively straightforward. I created a new forest and promoted the server to a domain controller.

I may also build a two-way trust with my friend's domain in the future.

NPS requires additional configuration. I currently use it as a RADIUS server for WPA2-Enterprise Wi-Fi authentication. For more details, see [Aruba AP](../../Networking/Aruba%20AP.md).

## NPS - Configuring a RADIUS Client

RADIUS authentication involves communication between a RADIUS client (wireless AP) and a RADIUS server (NPS), the RADIUS server checks the user's credentials against the directory service (AD) and applies the configured network policies to determine whether access should be granted.

Adding a RADIUS client to NPS allows the server to accept authentication requests from the configured network device. The client's IP address would be the management IP of the access point.

<img width="722" height="510" alt="NPS RADIUS client configuration" src="https://github.com/user-attachments/assets/c6c3ca79-a400-4e9a-a798-f8c08c987f1d" />

<img width="722" height="510" alt="NPS RADIUS client settings" src="https://github.com/user-attachments/assets/57ebc1d5-ec0e-4286-9592-52d9d71ba5d9" />

## NPS - Network Policy

Network policies define the conditions under which NPS grants or denies network access, along with the authentication methods and other settings required for the connection.

For my Wi-Fi network, a simple policy is to grant access only to users who belong to the wifi_access security group.

<img width="957" height="523" alt="NPS network policy configuration" src="https://github.com/user-attachments/assets/20f407a1-79cc-4d2d-8539-3e2b404ff73e" />

<img width="957" height="523" alt="NPS network policy conditions" src="https://github.com/user-attachments/assets/0d55482a-0692-4760-aaa0-4a722673e373" />

Under Conditions, configure the following:

- Windows Groups: The wifi_access security group.
- NAS Port Type: Wireless - IEEE 802.11.

These conditions ensure that the policy applies to members of the specified group connecting through a wireless network.

Under Constraints, select PEAP and EAP-MSCHAPv2 as the authentication method.

<img width="957" height="523" alt="NPS authentication method configuration" src="https://github.com/user-attachments/assets/d2854ad5-e44f-4706-8e19-14e674e5544c" />

<img width="957" height="523" alt="PEAP and EAP-MSCHAPv2 configuration" src="https://github.com/user-attachments/assets/3f49a327-229d-4fb0-b319-571b9c29a537" />

The remaining settings are left at their default values.

## Create Test Users and Groups

In Active Directory Users and Computers, create a security group named wifi_access and a test user. Add the user to the group.

<img width="450" height="475" alt="Active Directory user and group configuration" src="https://github.com/user-attachments/assets/e62ea6e0-e4b3-4c5f-8770-4a68839d2329" />

Users who belong to wifi_access can now authenticate to the Wi-Fi network

The client must also trust the CA that issued the NPS server certificate.

# Logging and Troubleshooting

The Windows Event Viewer provides a way to troubleshoot authentication issues. 
In the Event Viewer, navigate to Server Roles -> Network Policy and Access Services to view the NPS events. 

Here are some examples of events and the possible reason:

Event ID 18, shared secret mismatch. Verify that the shared secret configured on the access point matches the one configured in NPS:
<img width="775" height="326" alt="image" src="https://github.com/user-attachments/assets/cb02ac1a-5f0a-4678-b84a-c68f880ede35" />



Event ID 6273, client not trusting the RADIUS server's certificate or incorrect client configuration. Ensure that the client trusts the CA that issued the NPS server certificate:

<img width="753" height="606" alt="image" src="https://github.com/user-attachments/assets/aebbf733-20ba-4e99-9ead-902094c3dee0" />

<img width="787" height="630" alt="image" src="https://github.com/user-attachments/assets/1f64f451-1f78-4be7-b5f2-c437abe3f2ad" />


Event ID 6272, access granted:
<img width="829" height="377" alt="image" src="https://github.com/user-attachments/assets/1582716d-d449-4ad1-8f37-84f6afad0ae4" />

A log file is also located at C:\Windows\System32\LogFiles

