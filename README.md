<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2025 Data Center Azure Edition
- Windows 11 Pro, Version 25H2

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>

<p>

Step 1: 

Set up a resource group and virtual network/subnet specifically for the Active Directory environment in Azure.

- First, in a registered Azure subscription, create, name, select the proper time zone, and save a resource group that will house all resources that will be used for the Active Directory environment. These resources include virtual machines, virtual networks/subnets, virtual network interface cards, network security groups, etc.
<p>
  
<img width="907" height="887" alt="A-Create ResourceGrp " src="https://github.com/user-attachments/assets/96aae6ab-0ebd-4801-a86c-0bf6cd2266d5" />

</p>

- Second, create a virtual network (VNet) and subnet. Assign the VNet to the recently created resource group. Name and save it, using the same time zone as the assigned resource group.

<p> <img width="1550" height="447" alt="B-Create Vnet w Subnet" src="https://github.com/user-attachments/assets/31330f32-447a-428f-a7e6-0614968a0e0e" />

</p>

Step 2: 

Set up domain controller (DC) and client virtual machines. The domain controller VM will act as the server for Active Directory, and the client VM will be used to test the deployment. 

- First, create the domain controller VM. Assign the same recently created resource group to the VM. Name the virtual machine and set the time zone to the same as the resource group's. Set the "Image" of the VM to "Windows Server 2025 DataCenter Azure Edition" and select an appropriate size.
<p> 
  <img width="1147" height="717" alt="C-Create DC-2-VM" src="https://github.com/user-attachments/assets/28ffae7a-5f0e-4839-b1e2-4bedd41d2468" />
<img width="1032" height="676" alt="D-DC-2 Image Select" src="https://github.com/user-attachments/assets/4dbf4df2-a890-4677-9813-fdbb17ee9985" />

</p>

- Enter appropriate credentials under the "Administrator account" section. Make sure the selected inbound port is set to RDP 3389. *Do not save just yet.*

<p> 
  <img width="1192" height="745" alt="C-DC-2 Admin credentials" src="https://github.com/user-attachments/assets/8332aece-12f7-42d6-b7e2-95cc03f7c691" />

</p>

- Under the "Networking" tab, set the virtual network and subnet to the Vnet and subnet previously created. Now you can save/create the domain controller virtual machine.

<p> <img width="1122" height="547" alt="E-DC-2 Networking Vnet Settings" src="https://github.com/user-attachments/assets/26f82643-409a-414f-ab66-f6f31a7f3b86" />

</p>
</p>
- After domain controller VM is created, set the NIC private IP address to "static". Log into the VM and disable the Windows Firewall to test connectivity.

<p>
  
</p>

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
