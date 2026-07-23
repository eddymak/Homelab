# GNS3
GNS3 is a network emulation platform. Unlike other network simulation tools such as Cisco Packet Tracer, GNS3 runs real network device images, making it much more suitable for building and testing complex network topologies.

In this environment, we will deploy the GNS3 Remote VM on Proxmox and connect to it using the GNS3 Desktop client.

# Installation
## Installing GNS3 VM remote server on Proxmox
Download the [GNS3 for VMware ESXi](https://www.gns3.com/software/download-vm) image. 

Unzip it and you will find a OVA file. Proxmox cannot import OVA files directly, so we need to extract the VMDK disks from the OVA and import them into Proxmox.

1. Extract the disk 1 vmdk and disk 2 vmdk from the OVA and upload them to Proxmox using WinSCP or similar software.
 
3. Create a VM in Proxmox. Select "Do not use any media"
   <img width="622" height="238" alt="image" src="https://github.com/user-attachments/assets/875e6e30-374c-4f97-b280-d8ebf60947ee" />

4. On the created VM, Detech and Remove the hard disk    
   <img width="516" height="198" alt="image" src="https://github.com/user-attachments/assets/439773e6-6591-48b8-821d-f787fd60b4e0" />

5. In the below in Proxmox shell for both disk1 and disk2. Replace the VM ID (100 in the example below) with the ID of your VM.
   ```
   qm importdisk 100 GNS3_VM-disk1.vmdk local-lvm -format qcow2
   ```

6. 2 new hard disks should appear on the hardware list of the VM. Double click it, click Add and attach it.

7. Arrange the boot order. The smaller sized disk should be the first. 

   <img width="637" height="302" alt="image" src="https://github.com/user-attachments/assets/a29f84dd-8554-41aa-a18f-4a7d283ff481" />

8. Start the VM and GNS3 should be running. Configure the network settings using the GNS3 VM console.
   <img width="526" height="377" alt="image" src="https://github.com/user-attachments/assets/685cbee7-8cbc-4595-94c9-6783bf3869d5" />

## Connecting to GNS3 remote server using GNS3 Desktop client

1. Download the [GNS3 client](https://www.gns3.com/software/download)
2. Go to Preferences -> Server -> Main Server, enter the GNS3 VM host IP. By default, the server listens at TCP port 3080 without authentication    
   <img width="789" height="372" alt="image" src="https://github.com/user-attachments/assets/b3977442-012d-4a0c-b4fc-8b0d2f1ba754" />

Optionally [migrate project files to the new GNS3](https://gns3.com/heres-how-to-migrating-your-gns3-projects-and-images-to-a-new-gns3-vm)

# Troubleshooting
If you run into "KVM acceleration cannot be used" problem when starting appliances in projects, add the below lines into the /home/gns3/.config/GNS3/2.2/gns3_server.conf and try again:
```
[Qemu]
enable_kvm = false

```
