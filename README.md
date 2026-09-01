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

- Step 1: Set up a resource group and virtual network/subnet in Azure.
- Step 2: Set up domain controller (DC) and client virtual machines. 
- Step 3: Install Active Directory, create a domain admin user within domain forest & join client VM to the forest.
- Step 4: Step 4: Create Users and set up client VM for non-admin users (Remote Desktop for demo purposes)

<h2>Deployment and Configuration Steps</h2>

<p>

______________________________________________________________________________________________________________________________________________________________________


Step 1: 

Set up a resource group and virtual network/subnet specifically for the Active Directory environment in Azure.

<p>
<B>Set up resource group in Azure.<B/>  
</p><br/>

-  In a registered Azure subscription account, start on the home page and use the search bar to find Azure's resources and services.
-  Type, "resource groups" to navigate to the Resource groups page. (This page is found within the "Resource Manager" page.) Resource groups are created for the purpose of housing and organizing all resources that will be used within a given virtual deployment. In this case, this resource group will be assigned to the Active Directory deployment.
-  Click the "Create" button to create a new resource group. Name the resource group "Active Directory" and select the appropriate time zone. (Time zone should likely correspond to the location of users that will be created in the Active Directory deployment.)
-  You can use tags for organizing purposes but it is not necessary for demo purposes.
-  Select "Review + Create" and finalize to create the resource group. It will take a few seconds but creating the resource group, you should see its existence within the Resource Manager.

<p>
  <img width="970" height="362" alt="1-AD Home Page - Finding Resources" src="https://github.com/user-attachments/assets/0431d6ce-c079-4708-9d7a-ffc27062a834" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------
  
  <img width="962" height="506" alt="2-Find Resource Groups Mngr" src="https://github.com/user-attachments/assets/cb5c2e63-19a2-48c8-80ce-427a2d9a78b7" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1241" height="706" alt="3-Create Resource Group" src="https://github.com/user-attachments/assets/167e5365-5f83-467d-8741-c12399cd0799" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1035" height="536" alt="4 1-Name-Region Resource Grp" src="https://github.com/user-attachments/assets/aa5cab6b-e30b-49a0-8bfb-eadf57835f3e" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="547" height="825" alt="4-Name Review Create Resource Group" src="https://github.com/user-attachments/assets/6a89b90f-7f9e-44ab-bfc1-061d5176b9b2" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1366" height="437" alt="5-Proof of RsrceGrp" src="https://github.com/user-attachments/assets/0db689a6-65f2-42d6-a2ba-cd0818949346" /><br/>

  
</p>

-------------------------------------------------------------------------------------------------------------------------------------------------------------
  
<p>
<B>Set up virtual network with subnet in Azure.<B/>  
</p><br/>

- In the search bar, type "network foundation" and select it from the drop-down selection.
- On the side panel of the Network foundation page, select "Virtual networks" under the "Virtual network" drop-down tab. 
- Create a new virtual network (VNet).
- Assign the VNet to the recently created resource group.
- Name the Vnet "AD-Vnet" and select the same region that was used to create the "Active-Directory" resource group. (Leave all other settings at default unless specified by management.)
- Leave all the other settings under the different tabs set to default (Security, Address Space, etc.) 
- Review and create the virtual network. Upon its completion, it will automatically be assigned a subnet which can be found once you click on the virtual network's profile.
- Refresh the screen to return to the virtual networks page to view the Vnet. (Click virtual network's or any resource's name to view its content.)<br/>

<p>
  <img width="966" height="672" alt="7-Network Foundation for VNet" src="https://github.com/user-attachments/assets/7dedc70e-9a82-4e72-a098-3c38291aa1e1" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1072" height="596" alt="8-Select Virtual Network" src="https://github.com/user-attachments/assets/0c00b55c-95cb-4d97-a957-62fdf1fcdf7e" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1375" height="691" alt="9-Inside Virtual Network" src="https://github.com/user-attachments/assets/498645b0-2697-46a7-821a-e62de0509668" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1057" height="835" alt="10-Name Create Virtual Network" src="https://github.com/user-attachments/assets/4681fe00-7249-4b15-b1bf-c25f6fe0de4a" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="852" height="832" alt="11-Review Create Vnet" src="https://github.com/user-attachments/assets/ab8a2324-969c-4785-8ae6-7ce8172bb7ef" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1592" height="517" alt="12-Proof of Vnet and Subnet" src="https://github.com/user-attachments/assets/5083f142-cd25-4439-a122-281481c8f577" />


</p><br/>

___________________________________________________________________________________________________________________________________________________________________


Step 2: 

Set up domain controller (DC) and client virtual machines (VM). The domain controller VM will act as the server for Active Directory, and the client VM will be used to test the deployment of the Active Directory set up.<br/>  


<p>
<B>Set up Domain Controller using a virtual machine server in Azure.<B/>  
</p><br/>

- In the search bar in Azure, type "compute infrastructure" and select it in the drop-down selection to navigate to its page.
- On the side panel menu, click the "Infrastructure" tab and select "Virtual machines".
- From the virtual machines page, select "Create" to design a new virtual machine that will be used as the domain controller server for the Active Directory deployment.
- Azure has the option to select a classic virtual machine or a virtual machine scale set (VMSS). In this demo, I'll be using a classic virtual machine which can later be attached to a VMSS if needed.
- To appropriately design the domain controller, set the subscription to the appropriate one. Many real-world scenarios involve organizations to have multiple accounts for different departments, projects, etc.
- Set the resource group to the "Active-Directory" group that was created in the previous step.
- Name the virtual machine "DC-1" and match the region to the same region set to the "Active-Directory" resource group. For "Zone options" click "Self-selected zone".
- For the "Image" selection, Windows Server 2025 DataCenter: Azure Edition was used to operate the domain controller's system, which will later be used to configure and deploy Active Directory and Active Directory's proper resources.
- Selecting the size of the VM, a 2vCPU, 8GB RAM, with 10 data disks was appropriate for demonstration purposes.
- Under the "Administrator account" section, use an appropriate admin username (labuser) and password that will be the authorized administrator for the domain controller VM.
- Make sure RDP 3389 (remote desktop protocol, TCP port 3389) is selected for inbound ports. Read and confirm to licensing agreement. Do not create VM just yet.
- Navigate to the "Networking" tab and under "Virtual network" section, assign the virtual network to the AD-Vnet network that was created in step 1, and set the subnet to match the subnet that was created with AD-Vnet.
- Make sure the inbound port selection is set to RDP 3389 and leave all other settings at default, unless specified by management.
- Review + Create the virtual machine and finalize with the blue "Create" button. (Periodically refresh screen after each resource/service's creation.)
- Check the status and details of the VM once it has been deployed. After being created, the domain controller can be seen under the "Virtual Machines" page. 
  

<p>
<img width="982" height="657" alt="2-Setting Up VM via ComputeInfra" src="https://github.com/user-attachments/assets/a56b99a3-6d1c-4ff8-b52c-a400f5a3bc13" />  

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="732" height="737" alt="3-Select VM via Comp Infra" src="https://github.com/user-attachments/assets/0e9558f5-7c27-4fd0-b86a-3140c15dd7c1" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1382" height="702" alt="4-Inside Comp Infra" src="https://github.com/user-attachments/assets/d4cb6d5f-00f7-4d4b-a1e0-5e04340b04e6" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1370" height="752" alt="5-Creating a VM step 1" src="https://github.com/user-attachments/assets/5d13339f-861a-4ccb-8580-7fdc7c70e855" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1090" height="672" alt="6-Assgn Resrce Grp to VM" src="https://github.com/user-attachments/assets/c3f65c8f-c6d9-4815-8153-981e1f35c7da" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1232" height="907" alt="7-Name and Zone VM" src="https://github.com/user-attachments/assets/b6c73c36-3aec-4195-9022-f4f0f8dfebb7" />

   -------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1460" height="832" alt="8-Select Image for DC-1 VM" src="https://github.com/user-attachments/assets/214b4da9-88cd-42e2-8cef-9ee03fd54c7d" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1492" height="757" alt="9-Image Sizing DC-1 VM" src="https://github.com/user-attachments/assets/b2799d4f-73b8-4a5b-8295-f943009c0ab8" />

 -------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1042" height="737" alt="10-Image Sizing DC-1 VM2" src="https://github.com/user-attachments/assets/00f16e46-97be-4b10-883a-2b281e813d69" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1165" height="910" alt="11-Admin Credentials DC1 VM" src="https://github.com/user-attachments/assets/e2788ea2-c5a3-49ed-9e30-e6ddc34d9463" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1186" height="902" alt="12-DC1 VM ADVnet Routing" src="https://github.com/user-attachments/assets/395f5480-465b-490e-9c86-ef6581436f7a" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1221" height="921" alt="13-Review Create DC1 VM" src="https://github.com/user-attachments/assets/57fc538e-3979-409a-9270-3224c0ca567f" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1212" height="547" alt="14-Create VM Shortcut" src="https://github.com/user-attachments/assets/a64c5c9a-c9f4-4624-ba53-c4e30a6a67f9" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1647" height="817" alt="15-Inside DC1 VM " src="https://github.com/user-attachments/assets/a9414202-468b-4fc8-9f4d-5ee54277cd71" />

</p><br/>

___________________________________________________________________________________________________________________________________________________________________
<p>
<B>Set up Client VM using a virtual machine in Azure.<B/>  
</p><br/>

- In this demo, the Client VM will be used to test the connectivity and accuracy of the Active Directory deployment. The Active Directory software and resources will be downloaded and deployed on the DC-1 domain controller using the Server Manager within the virtual machine server. 
- As a shortcut, start in the search bar and type in "virtual machines" and navigate to the virtual machines page.
- Create a new virtual machine by clicking the "Create" button (classic VM, not VMSS for the demo).
- To appropriately design the Client VM, set the subscription to the appropriate one. Many real-world scenarios involve organizations to have multiple accounts for different departments, projects, etc.
- Set the resource group to the "Active-Directory" group that was created in the previous step.
- Name the virtual machine "client-1" and match the region to the same region set to the "Active-Directory" resource group. For "Zone options" click "Self-selected zone".
- For this virtual machine, Windows 11 Pro, Version 25H2 x64 was used as the VM's image/operating system.
- Selecting the size of the VM, a 2vCPU, 8GB RAM, with 10 data disks was appropriate for demonstration purposes.
- Under the "Administrator account" section, use an appropriate admin username (labuser) and password that will be the authorized administrator for the domain controller VM.
- Make sure RDP 3389 (remote desktop protocol, TCP port 3389) is selected for inbound ports. Read and confirm to licensing agreement. Do not create VM just yet.
- Navigate to the "Networking" tab and under "Virtual network" section, assign the virtual network to the AD-Vnet network that was created in step 1, and set the subnet to match the subnet that was created with AD-Vnet.
- Make sure the inbound port selection is set to RDP 3389 and leave all other settings at default, unless specified by management.
- Review + Create the virtual machine and finalize with the blue "Create" button. (Periodically refresh screen after each resource/service's creation.)
- Check the status and details of the VM once it has been deployed. After being created, the domain controller can be seen under the "Virtual Machines" page. 

<img width="1427" height="911" alt="16-Name Client1 VM with RGrp" src="https://github.com/user-attachments/assets/5690de1a-306e-4343-a8ef-8ffb45d52540" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1221" height="917" alt="17-Select Client VM Image and Size" src="https://github.com/user-attachments/assets/e4501682-f0fa-4708-a34f-025a1799156c" />

  -------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1360" height="926" alt="18-Admin Crdntials Client VM" src="https://github.com/user-attachments/assets/7e095576-7e1f-43e7-a0f5-c89eee223555" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1222" height="906" alt="19-License Agreement Check" src="https://github.com/user-attachments/assets/3ba9fa57-384b-4ffe-8fec-5a1719cb1345" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1222" height="921" alt="20-Networking Settings Client VM" src="https://github.com/user-attachments/assets/0338705c-c801-4f79-a081-bd5a36344ccb" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------  

<img width="1321" height="940" alt="21-Review Create Client VM" src="https://github.com/user-attachments/assets/9ee6ffad-17c2-4e72-ac4d-fbc45b42c7f4" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------  

<p>
</p><br/>

___________________________________________________________________________________________________________________________________________________________________


<B>Sidestep: Configure settings for consistent connectivity between DC-1 and client-1 VMs.<B/> 

This is a two-part process that involves configuring the domain controller's (DC-1) network interface card (NIC) private IP address to "static" and to configure client-1 VM's DNS settings so that it is mapped to DC-1's private IP address. The connectivity will be tested in the later steps. 

- To configure DC-1's IP settings, navigate to the "compute infrastructure-virtual machines" page in Azure and click on the DC-1 VM.
- Within DC-1, on the side panel menu, under the "Networking" tab, click on "Network settings".
- In the "network interface/ip configuration" box on the top, click on the "dc-1637/ipconfig1" link to go inside and configure the NIC.
- Near the bottom of the page, click on the "ipconfig1" link and set private IP address settings to "static" and click "Save".
- After refreshing the screen, you should be able to see that the Private IP address is set to "static". The VM's private IP address should automatically populate in the text box. Copy this IP address to be used in client-1's DNS configuration.  
- Navigate back to the "compute infrastructure-virtual machines" page and click on client-1 VM.
- Within client-1, on the side panel menu, under the "Networking" tab, click on "Network settings", then click the NIC/IP configuration link "client-1499/ipconfig1".
- Click on "ipconfig1" link and navigate to "DNS servers" under the "Settings" tab.
- Select "custom" DNS server and enter the private IP address of DC-1 VM and hit the "apply" button.




<p>
<img width="1667" height="887" alt="1-DC1-Network settings" src="https://github.com/user-attachments/assets/89f30f6b-890d-409f-bd61-ccd2a8662173" />

----------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1897" height="912" alt="2-DC1-NIC-Static" src="https://github.com/user-attachments/assets/44f8c3ff-a665-4ba2-894a-2c934f4f052f" />

----------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1780" height="787" alt="3-DC1-Proof of Static Private IP " src="https://github.com/user-attachments/assets/b2965807-8b8d-4cfb-bbca-a6d0aa7f2f6c" />

----------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1832" height="860" alt="4-client1-Network settings" src="https://github.com/user-attachments/assets/00df4de6-6598-4939-8b1e-003324dfe56a" />

----------------------------------------------------------------------------------------------------------------------------------------------------------


<img width="1376" height="920" alt="6-client1-DNS config" src="https://github.com/user-attachments/assets/8a5f8bea-9440-48f0-8156-d548af22c8d8" />

----------------------------------------------------------------------------------------------------------------------------------------------------------

</p>
<br/>

<p> Steps 1-2 with the above sidestep is the infrastructure stage of setting up virtual machines to be used by and with Active Directory. The last steps will show how to download Active Directory, properly deploy it within the cloud infrastructure, and using PowerShell to create authorized users who can login to the client-1 VM. </p><br/> 

_________________________________________________________________________________________________________________________________________________
  

<p>

</p>
<br />
Step 1: 

Step 3: Install Active Directory (AD), create a domain admin user within domain forest & join client VM to the forest. 
A forest is a group of related AD domains under one overall directory system and is an administrative boundary that enforces and prioritizes security. 
Active Directory will be installed on the domain controller (DC-1) via the Server Manager program that comes preinstalled on Windows Server 2025. Before deployment, it's important to "promote" the server to actually be recognized as the domain controller. Naming a server "domain controller" doesn't automatically make it a domain controller. It must be promoted. To perform installation and promotion on the server and to join client-1 VM to the forest, use Remote Desktop Connection application from the host computer (usually preinstalled on Windows) to login to either of the VMs using their respective PUBLIC IP addresses. 

<p>
<B>Install Active Directory on DC-1 and promote server to domain controller via the Server Manager.<B/>  
</p><br/>

- In Azure, go to the Virtual Machines page, then locate and copy the public IP addresses of DC-1 and client-1 to be used with Remote Desktop Connection. (Use notepad or notes app to paste the IP addresses for reference purposes.)
- Then, on the host computer, open the Remote Desktop Connection app. Right click on the "Start" icon, select "Run" and type in mstsc.exe to open Remote Desktop or search for it by name in the Windows "Start" search bar.
- In the Remote Desktop window, enter the public IP address of DC-1 in the "computer" text bar and press "connect".
- Windows Security will ask for credentials, likely using the host computer username, which needs to be fixed because the goal is to login as "labuser" admin to access DC-1 server.
- Click the "More choices" link, click "Use a different account" and enter the admin credentials used to create the DC-1 virtual machine server and click "ok". For demo purposes, the admin username is "labuser" and the password is "Cyberworld123!" (click yes, and/or exit out of any popup window).
- These steps should permit the user access into DC-1 virtual machine server, which Active Directory will be installed on. 
