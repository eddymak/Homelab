# Wireless AP Cannot Connect to the Cloud After Firewall Migration

The Instant On AP failed to connect to the Instant On cloud through the OPNsense firewall

# Symptoms

- AP status LED solid amber

  <img width="500" alt="image" src="https://github.com/user-attachments/assets/0bd3b185-9125-405f-bfb7-f4030d4f3145" />

- AP Web GUI displays the error INTERNAL_CONNECTION_TERMINATION

  <img width="1158" height="327" alt="image" src="https://github.com/user-attachments/assets/73bc9803-8b3b-494a-bf08-533f919f7f61" />

- The Instant On Cloud Portal shows the AP as Disconnected

# Root Cause

The outbound NAT rule on the OPNsense firewall was incorrectly configured

The rule matched a single host address instead of the management subnet, so traffic from the AP was not source NATed before leaving the WAN interface.

# Diagnosis

## 1. Verify Layer 2

Confirm the switch configuration is correct

- The trunk port connected to the AP uses VLAN 99 as the native (PVID) VLAN.

  <img width="1471" height="348" alt="image" src="https://github.com/user-attachments/assets/a0893949-7ddd-4f9a-be16-fd0f5cfec149" />

- VLAN 99 is permitted on the trunk between the switch and the firewall.

  <img width="1314" height="474" alt="image" src="https://github.com/user-attachments/assets/ce1a13be-4f09-46a4-a769-79a07531ca3a" />

- Verify the switch has learned the AP's MAC address.

  ```
  /interface/bridge/host/print
  ```

  <img width="906" height="163" alt="image" src="https://github.com/user-attachments/assets/4b521943-df85-40f5-bd1f-96ad67f801e7" />

## 2. Verify DHCP

Confirm the AP successfully receives an IP address from the DHCP

<img width="1887" height="408" alt="image" src="https://github.com/user-attachments/assets/8d1d66c7-fb6a-49e3-a0f7-0f3418a278ee" />


## 3. Review Firewall Logs

Check the firewall logs to verify outbound traffic from the AP is permitted

<img width="1096" height="588" alt="image" src="https://github.com/user-attachments/assets/ec7a19fd-5f2e-4280-997d-c12a0c8a5c91" />

## 4. Verify Outbound NAT

Review the outbound NAT configuration.

The outbound NAT rule exists, but the Source field is configured as Address instead of Networ, so traffic from the subnet did not match the NAT rule and is not translated.

<img width="1786" height="366" alt="image" src="https://github.com/user-attachments/assets/0179266e-1eb5-42ed-8c06-05c4aa2ceb2e" />

# Resolution

Modify the outbound NAT rule so that the Source matches the network rather than the interface address

<img width="1765" height="353" alt="image" src="https://github.com/user-attachments/assets/238e7d3e-25e6-4818-8cb3-aa9c19978426" />

After applying the change:

- The firewall log shows the outbound NAT rule is being matched

  <img width="1771" height="679" alt="image" src="https://github.com/user-attachments/assets/63825fa2-b531-400f-a15d-33c1f77ab282" />

- The AP successfully reconnects to the Instant On cloud and downloads its latest configuration

  <img width="1185" height="292" alt="image" src="https://github.com/user-attachments/assets/17143220-8fbd-4d18-bac9-9df078c84f4c" />

# Lesson Learnt

I didn't know that when NAT rules are matched there would be logs in the live view. These log entries are useful for confirming that traffic is being translated by the expected NAT rule.

I setup log search filter was created to display only outbound NAT log entries for future troubleshooting.
