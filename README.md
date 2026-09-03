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
- Step 3: Install Active Directory, create a domain admin user within new domain forest & join client VM to the forest.
- Step 4: Step 4: Set up Remote Desktop for non-admin users to access client-1 VM and create users in PowerShell ISE. 

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
- Check the status and details of the VM once it has been deployed. After being created, the domain controller can be seen under the "Virtual Machines" page.<br/>
  

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
- Check the status and details of the VM once it has been deployed. After being created, the domain controller can be seen under the "Virtual Machines" page.<br/> 

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

This is a two-part process that involves configuring the domain controller's (DC-1) network interface card (NIC) private IP address to "static" and to configure client-1 VM's DNS settings so that it is mapped to DC-1's private IP address. The default of the virtual network for both DC-1 and client-1 is mapped so that client-1 going to Azure's Vnet DNS server, but the following steps will map client-1 to the domain controller, DC-1. So, whenever client-1 needs to look up any domain, i.e. microsoft.com, it will look to DC-1 server. Also, these steps allow client-1 VM to join the forest domain in later steps. On DC-1, turn the Windows Firewall state to "off" to test DNS connectivity between the two VMs.

- To configure DC-1's IP settings, navigate to the "compute infrastructure-virtual machines" page in Azure and click on the DC-1 VM.
- Within DC-1, on the side panel menu, under the "Networking" tab, click on "Network settings".
- In the "network interface/ip configuration" box on the top, click on the "dc-1637/ipconfig1" link to go inside and configure the NIC.
- Near the bottom of the page, click on the "ipconfig1" link and set private IP address settings to "static" and click "Save".
- After refreshing the screen, you should be able to see that the Private IP address is set to "static". The VM's private IP address should automatically populate in the text box. Copy this IP address to be used in client-1's DNS configuration.  
- Navigate back to the "compute infrastructure-virtual machines" page and click on client-1 VM.
- Within client-1, on the side panel menu, under the "Networking" tab, click on "Network settings", then click the NIC/IP configuration link "client-1499/ipconfig1".
- Click on "ipconfig1" link and navigate to "DNS servers" under the "Settings" tab.
- Select "custom" DNS server and enter the private IP address of DC-1 VM and hit the "apply" button.
  
- <br/>

- Login to DC-1 server using admin credentials (labuser, Cyberworld123!) right click on Windows "Start" menu, and select "Run" and enter "wf.msc" to open Windows Defender firewall.
- Navigate and click "Windows Defender Firewall Properties".
- Turn the firewall state for each profile - domain profile, private profile, public profile - to "off". Apply and confirm the settings.
- Login to client-1 VM using admin credentials, open Windows PowerShell to run as administrator, and ping DC-1's private IP address (10.0.0.4).
- Using the "ping" bash in PowerShell to call to/connect to DC-1 will show that the NIC configuration settings were properly stored and client-1 will look to DC-1's DNS server when searching for a domain or ip address.
- Last, in client-1's PowerShell, run the command "ipconfig /all" to display full details of every NIC on the host VM. You will see that the DNS server recognized is the private IP address of DC-1 VM.<br/>




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

<img width="1252" height="950" alt="24-DC1-Windows firewall on" src="https://github.com/user-attachments/assets/97dc514e-b422-4de1-920c-afa7b4815223" />

----------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1280" height="917" alt="25-Windows firewall properties" src="https://github.com/user-attachments/assets/fdc71393-55aa-43b5-a04f-500ed83ea58f" />

----------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1070" height="916" alt="26-Windows firewall profile state" src="https://github.com/user-attachments/assets/f95cefdd-3bec-4c92-931e-4076c906e8d5" />

----------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1257" height="931" alt="27-DC1 Windows FW off" src="https://github.com/user-attachments/assets/88d61cdb-38f4-425c-aeb9-58dbac642981" />

----------------------------------------------------------------------------------------------------------------------------------------------------------


<img width="1367" height="970" alt="28-client1-powershell " src="https://github.com/user-attachments/assets/09e5a75b-0510-4a51-9d63-5a963b5dcd2f" />

----------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1221" height="852" alt="29-client1-ping dc1 private ip" src="https://github.com/user-attachments/assets/7cd9e2a4-d376-4923-a303-7b4b62ac939e" />

----------------------------------------------------------------------------------------------------------------------------------------------------------


<img width="1247" height="932" alt="30-client1 ipconfig all" src="https://github.com/user-attachments/assets/2eb13e75-2396-4005-a6f9-9cbb7a192fb3" />

----------------------------------------------------------------------------------------------------------------------------------------------------------


</p>
<br/>

<p> Steps 1-2 with the above sidestep is the infrastructure stage of setting up virtual machines to be used by and with Active Directory. The last steps will show how to download Active Directory, properly deploy it within the cloud infrastructure, and using PowerShell to create authorized users who can login to the client-1 VM. </p><br/> 

_________________________________________________________________________________________________________________________________________________
  

<p>

</p>
<br /> 

Step 3: 
<p>
  
Install Active Directory (AD), create a domain admin user within domain forest & join client VM to the forest. 
</p>
 <br/>

A forest is a group of related AD domains under one overall directory system and is an administrative boundary that enforces and prioritizes security. 
Active Directory will be installed on the domain controller (DC-1) via the Server Manager program that comes preinstalled on Windows Server 2025.<br/>

Before deployment, it's important to "promote" the server to actually be recognized as the domain controller. Naming a server "domain controller" doesn't automatically make it a domain controller. It must be promoted. To perform installation and promotion on the server and to join client-1 VM to the forest, use Remote Desktop Connection application from the host computer (usually preinstalled on Windows) to login to either of the VMs using their respective PUBLIC IP addresses.<br/>

<br/>
<p>
<B>Install Active Directory on DC-1 and promote server to domain controller via the Server Manager.<B/>  
</p>

- In Azure, go to the Virtual Machines page, then locate and copy the public IP addresses of DC-1 and client-1 to be used with Remote Desktop Connection. (Use notepad or notes app to paste the IP addresses for reference purposes.)
- Then, on the host computer, open the Remote Desktop Connection app. Right click on the "Start" icon, select "Run" and type in mstsc.exe to open Remote Desktop or search for it by name in the Windows "Start" search bar.
- In the Remote Desktop window, enter the public IP address of DC-1 in the "computer" text bar and press "connect".
- Windows Security will ask for credentials, likely using the host computer username, which needs to be fixed because the goal is to login as "labuser" admin to access DC-1 server.
- Click the "More choices" link, click "Use a different account" and enter the admin credentials used to create the DC-1 virtual machine server and click "ok". For demo purposes, the admin username is "labuser" and the password is "Cyberworld123!" (click yes, and/or exit out of any popup window).
- These steps should permit the user access into DC-1 virtual machine server, which Active Directory will be installed on.
- While in DC-1, navigate to and open "Server Manager" by typing the name in the Windows "Start" search bar.
- In the Server Manager dashboard, under "Configure this local server", click "Add roles and features".
- Follow the prompts as follows: Click "next" from the "Before you begin" page to "Installation type" and select "role-based or featured based".
- On the "Server selection" page, select DC-1 from the server pool and click "next".
- For "Server roles", click "Active Directory Domain Services" and click "Add features".
- Click "next" through the "features", "AD DS" and "Confirm install" pages. Click "Restart after install" box and install Active Directory.
- After a successful install, proof of Active Directory Domain Services will be displayed on the Server Manager dashboard as "AS DS".
- Next, promote DC-1 to domain controller from the Server Manager. On the top corner of Server Manager is a flag with a caution alert icon.
- Click on the flag alert and click "Promote this server as domain controller".
- From here, the Active Directory Domain Services configuration wizard window will guide the installation.
- On the "deployment configuration" page, select "Add new forest". For demo purposes, "mydomain.com" is used as the root domain name.
- Enter a Directory Services Restore Mode (DSRM) password that would be used by the authorized admin only. For this demo, the password is "Password1".
- Uncheck "Create DNS delegation" on "DNS Options" page. Click "next" to the "Additional options" page where the NetBIOS domain name will automatically populate, then click "next".
- Click "next" through the "paths" and "review options" pages. Once on the "prerequisite check" page, if all steps above were properly followed, the prerequisite check should be successful. Then click "install".
- After a successful install, DC-1 will restart to give the virtual machine time to catch up to the new installations.
- Restart and then log back into DC-1 as username: mydomain.com\labuser with the same admin credentials password. (Password123!)<br/>

______________________________________________________________________________________________________________________________________________________


<p>
  <img width="1717" height="512" alt="1-Copy VMs Public IP Addresses " src="https://github.com/user-attachments/assets/62fd5d00-3ee6-4508-918d-ac2e8bf6ea01" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="837" height="372" alt="2-Run RDC " src="https://github.com/user-attachments/assets/0a572dba-0e8d-4720-bd66-47da12be8c01" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="756" height="721" alt="3-DC1 IP Addr RemoteDsktp " src="https://github.com/user-attachments/assets/35cb304b-fb6c-4b3e-91c5-b4298a7784c9" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="655" height="782" alt="4-DC1 Login pt1" src="https://github.com/user-attachments/assets/b204594a-1e2b-4689-8d38-3f78fe08fa49" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="662" height="930" alt="5-AdminCredDC1" src="https://github.com/user-attachments/assets/57f070a0-ed2a-4edc-8dba-932dbd6ba659" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1191" height="842" alt="6- Open ServerManager" src="https://github.com/user-attachments/assets/62fca83c-7a53-4912-829b-4c8bda191ed5" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1326" height="977" alt="7-Addroleandfeatures step1" src="https://github.com/user-attachments/assets/79d80d79-7d2d-4ac1-88ff-91db2ab82fd4" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1335" height="975" alt="8-install type role features" src="https://github.com/user-attachments/assets/89ceab02-28a8-471f-a217-fc1111f81d1d" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1331" height="961" alt="9-Select servers DC1" src="https://github.com/user-attachments/assets/2af790f9-1c78-4c5a-bc44-97b1b2c76a1a" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1276" height="932" alt="10-AD Domain Services" src="https://github.com/user-attachments/assets/b230d3b0-34b4-4de3-831f-a1de8e263f41" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1157" height="935" alt="11-Domain Services Add features" src="https://github.com/user-attachments/assets/47bd4247-4833-4c06-99cc-870317746380" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1165" height="911" alt="12-Features AD " src="https://github.com/user-attachments/assets/b4eb1922-9edd-47f1-a2e7-0bd7b9af7fcb" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1242" height="967" alt="13-AD DS page " src="https://github.com/user-attachments/assets/24a3cd29-a596-4fcf-b644-48db8aeeaddb" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1276" height="902" alt="14-Confirm Install Restart " src="https://github.com/user-attachments/assets/c5c00591-40b9-461e-a0f9-daa5029e083a" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1240" height="937" alt="15-AD Install" src="https://github.com/user-attachments/assets/ff8b2982-e087-4db4-9bb3-bf057cfd6204" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1267" height="827" alt="16-Proof of AD DS" src="https://github.com/user-attachments/assets/b3eb8fbf-ca72-4125-a533-84657127300e" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1467" height="817" alt="17-Promote this server " src="https://github.com/user-attachments/assets/9fcb9914-b6eb-4cfa-839c-e94a6cad6a08" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1522" height="952" alt="18-Add new forest" src="https://github.com/user-attachments/assets/fe149d47-573a-4405-b8cb-232c3dfbce20" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1331" height="887" alt="19-DC Options Password1" src="https://github.com/user-attachments/assets/ba04c813-76ce-4fde-9e9f-8bcc92a7d51b" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1182" height="890" alt="20-Uncheck Create DNS " src="https://github.com/user-attachments/assets/7650753d-47c0-4ebd-a6fc-7502ee417103" />

  --------------------------------------------------------------------------------------------------------------------------------------------------
 
  <img width="1262" height="942" alt="21-NetBIOS " src="https://github.com/user-attachments/assets/ffa52b87-3192-4d62-8d25-7b8c5a1db6ca" />
  
  --------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1271" height="912" alt="22-Paths " src="https://github.com/user-attachments/assets/6c037589-adbe-4ac0-a509-205390beec4c" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1272" height="955" alt="23-review options view script" src="https://github.com/user-attachments/assets/4f16d3d0-b9b7-4201-b042-adb4162d5143" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------


  <img width="1410" height="935" alt="24-Prerequesite check" src="https://github.com/user-attachments/assets/d3f6f9d3-38db-4a65-9b18-4cb8518d16df" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="621" height="756" alt="25- mydomain com-labuser login" src="https://github.com/user-attachments/assets/38e4168c-d64e-4c49-9b69-43e01ee1f915" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  


</p>
<br/>

<p>
<B>Create a domain admin user within the new forest domain.<B/>  
</p>
<br/>

- After logging back into DC-1 as "mydomain.com\labuser", open Active Directory Users and Computers (ADUC) from the Windows "Start" search bar.
- In ADUC, right click the domain forest "mydomain.com" and create a new Organizational Unit (OU) called "_EMPLOYEES"
- Create another new OU called "_ADMINS". After its creation, right click on _ADMINS and create a new user named "Jane Doe" with the username "jane_admin" and password "Cyberworld123!".
- Add jane_admin to the "Domain Admins" security group by right clicking on Jane Doe's name and clicking "add to group".
- Type in Domain Admins and check the name, then click "Ok" to add Jane Doe to Domain Admins security group.
- Logout of DC-1 as mydomain.com\labuser and log back in as "mydomain.com\jane_admin" with "Cyberworld123!" as the password. (Check system account by right clicking on Windows "Start" and click "Systems" to see details of the virtual machine.)
- These steps demonstrate creating an administrator user of the domain controller who has access to manipulate Active Directory.<br/>


<p>
  <img width="1232" height="862" alt="26-Open ADUC " src="https://github.com/user-attachments/assets/1009d4a4-b36c-470c-890d-d0eb3396b7a0" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1182" height="882" alt="27-Create New OU " src="https://github.com/user-attachments/assets/8e4875d8-bba0-4f63-a05f-1b9a62bba36b" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1212" height="832" alt="28- OU _EMPL" src="https://github.com/user-attachments/assets/7e3935ea-885a-49f9-b14b-999fb2e9d86b" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1197" height="812" alt="29-Create New Admin User" src="https://github.com/user-attachments/assets/0bea50be-633b-48df-b3d0-3445dfe642d4" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1241" height="792" alt="29-OU _ADMINS" src="https://github.com/user-attachments/assets/9a9d0b0e-b99a-40d9-a1d0-1a57d5db9280" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1212" height="811" alt="30-Jane_Admin_passwd" src="https://github.com/user-attachments/assets/a9709d2b-e5fa-4d98-8b26-9d0ab45c472d" />


  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1161" height="846" alt="31-Add Jane To Grp" src="https://github.com/user-attachments/assets/5f4dfaf8-515c-4ff0-a80e-ee7e2444a819" />

  
---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1232" height="867" alt="32-Add Jane Domain Grp" src="https://github.com/user-attachments/assets/722f6dee-b7f6-4808-a4e1-a0615b68a9fc" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------
  


  <img width="1215" height="830" alt="33-Proof of Grpship" src="https://github.com/user-attachments/assets/e74c0d91-34b7-4aeb-add9-29f3730396f5" />


  ---------------------------------------------------------------------------------------------------------------------------------------------------


  <img width="1166" height="841" alt="34-Proof of Jane_Admin Login DC1" src="https://github.com/user-attachments/assets/2a2c1aa8-3b29-4b14-9d82-3d94042267dd" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

</p>
<br/>

</p>
<br/>

<p>
<B>Join client-1 to the domain forest.<B/>  
</p>
<br/>

- Login to client-1 VM as the original "labuser" user and join the VM to mydomain.com forest. 
- Right click Window's "Start" menu, select "System". Click "Advanced system settings".
- Click "Change" to change the VM's/computer's domain, then enter "mydomain.com" and add it as the domain. When prompted, enter for Jane Doe's domain admin credentials (mydomain.com\jane_admin, Cyberworld123!).
- This step will welcome client-1 VM to join the AD domain created in DC-1 server.
- Confirm that client-1 is recognized as a computer system within ADUC under the "computers" tab of "mydomain.com" domain forest.<br/>

<p>

<img width="1270" height="930" alt="35-client1 system menu" src="https://github.com/user-attachments/assets/12765746-588f-4291-b5c8-34ac11f0ac19" />

---------------------------------------------------------------------------------------------------------------------------------------------------

<img width="877" height="771" alt="36-Advanced system settings" src="https://github.com/user-attachments/assets/4ca03017-d356-48b6-87a5-b1830bee7c3e" />

---------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1002" height="932" alt="37-system properties " src="https://github.com/user-attachments/assets/f2307121-a40e-4500-8ed3-b27db7dac4b5" />

---------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1255" height="957" alt="38-join client-1 to domain controller" src="https://github.com/user-attachments/assets/314de323-b76c-4a05-8452-a6f7aa81404d" />

---------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1110" height="837" alt="39-welcome to domain - confirmation" src="https://github.com/user-attachments/assets/c3269028-871c-4c40-9fdc-e3eeb7243818" />

---------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1057" height="915" alt="40-verify client-1 joined mydomain " src="https://github.com/user-attachments/assets/637cee03-a30c-476c-b534-8ef22ce0f7a2" />


---------------------------------------------------------------------------------------------------------------------------------------------------
  
</p>
<br/>



<p>
  
Step 4:Set up Remote Desktop for non-admin users to access client-1 VM and create users in PowerShell ISE from the domain controller.
</p>
 <br/>

In these steps, we'll see how to enable remote desktop for non-administrative authorized users to access client-1 VM. Then once the configuration is complete, we'll create users to be added to the forest domain we created in Active Directory by running a coded script in Windows PowerShell ISE. 

<B>Set up Remote Desktop for non-admin users.<B/>

- Login to client-1 as mydomain.com\jane_admin, open system systems and navigate to "Remote Desktop" page and click "Remote Desktop users".
- Click "Add" and enter "domain users" (a group within AD) and apply settings. Logout of client-1 as jane_admin.
- Now, any authorized user that is within the "domain users" forest domain will have non-administrative access to login to client-1 VM. <br/>

<p>

  <img width="1077" height="910" alt="1-system remote desktop settings" src="https://github.com/user-attachments/assets/00ac2d9a-5c48-49d1-aac1-61ac94868c47" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1210" height="782" alt="2-add user groups to remote desktop" src="https://github.com/user-attachments/assets/9b51aad9-d81e-4d51-a6c4-8240848d5680" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1015" height="772" alt="3-domain users added proof" src="https://github.com/user-attachments/assets/5dc0807e-57da-49a9-922c-323c3660fb22" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------


</p>
<br/>

<B>Create new users using Windows PowerShell ISE.<B/>

- Login to DC-1 as jane_admin, navigate to Windows PowerShell ISE and run as administrator.
- Create a new file, name and save it, then paste the coded script and "run" the script.
- Next, open ADUC and verify created users within mydomain.com forest under the "_EMPLOYEES" organizational unit.
- Attempt to login with one of the new users to confirm proper configuration. <br/>

<p>

  <img width="1097" height="950" alt="4-open powershell ISE " src="https://github.com/user-attachments/assets/811ed8ee-ff44-4205-960c-a20a9791ec9f" />
  
---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1066" height="722" alt="5-insert script AD " src="https://github.com/user-attachments/assets/86968187-6abd-459e-9bb2-82f1111f0b21" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1062" height="702" alt="6-run script " src="https://github.com/user-attachments/assets/d8c6f874-b885-49b1-9feb-13a394ec23f7" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1112" height="832" alt="7-Created Users" src="https://github.com/user-attachments/assets/eefd1cc9-8723-4839-bff8-12c6c57f7355" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1136" height="847" alt="8-AD added users in domain" src="https://github.com/user-attachments/assets/ed901706-b1b7-4e65-9367-85c99eab0a10" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="620" height="726" alt="9-login as non-admin" src="https://github.com/user-attachments/assets/5e28459a-9658-45fe-b21e-3e65832bbb29" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------

  <img width="1031" height="807" alt="10-Proof of Non-admin login" src="https://github.com/user-attachments/assets/6110f8ee-6db0-4e15-85f9-2b8b016ee6f2" />

  ---------------------------------------------------------------------------------------------------------------------------------------------------



</p>


