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

Set up a resource group and virtual network/subnet for a Domain Controller (DC) and a Client-1 virtual machine for Active Directory deployment in Azure.

- First, in a registered Azure subscription, create, name, select the proper time zone, and save a Resource Group that will house all resources that will be used for the Active Directory environment. These resources include virtual machines, virtual networks/subnets, virtual network interface cards, network security groups, etc.
<p>
  
<img width="907" height="887" alt="A-Create ResourceGrp " src="https://github.com/user-attachments/assets/96aae6ab-0ebd-4801-a86c-0bf6cd2266d5" />

</p>

- Second, create a virtual network and subnet. Assign the VNet to the recently created resource group. Name and save it, using the same time zone as the assigned resource group.

<p> <img width="1550" height="447" alt="B-Create Vnet w Subnet" src="https://github.com/user-attachments/assets/31330f32-447a-428f-a7e6-0614968a0e0e" />

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
