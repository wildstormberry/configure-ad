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
- Review and create the virtual network. Upon its completion, it will automatically be assigned a subnet which can be found once you click on the virtual network's profile. (Click virtual network's or any resource's name to view its content.)<br/>

<p>
  <img width="966" height="672" alt="7-Network Foundation for VNet" src="https://github.com/user-attachments/assets/7dedc70e-9a82-4e72-a098-3c38291aa1e1" />

  <img width="1072" height="596" alt="8-Select Virtual Network" src="https://github.com/user-attachments/assets/0c00b55c-95cb-4d97-a957-62fdf1fcdf7e" />

  <img width="1375" height="691" alt="9-Inside Virtual Network" src="https://github.com/user-attachments/assets/498645b0-2697-46a7-821a-e62de0509668" />

  <img width="1057" height="835" alt="10-Name Create Virtual Network" src="https://github.com/user-attachments/assets/4681fe00-7249-4b15-b1bf-c25f6fe0de4a" />

  <img width="852" height="832" alt="11-Review Create Vnet" src="https://github.com/user-attachments/assets/ab8a2324-969c-4785-8ae6-7ce8172bb7ef" />

  <img width="1592" height="517" alt="12-Proof of Vnet and Subnet" src="https://github.com/user-attachments/assets/5083f142-cd25-4439-a122-281481c8f577" />


</p><br/>

___________________________________________________________________________________________________________________________________________________________________


Step 2: 

Set up domain controller (DC) and client virtual machines. The domain controller VM will act as the server for Active Directory, and the client VM will be used to test the deployment. The virtual server that will be used for  

- First, create the domain controller VM. Assign the same recently created resource group to the VM. Name the virtual machine and set the time zone to the same as the resource group's. Set the "Image" of the VM to "Windows Server 2025 DataCenter Azure Edition" and select an appropriate size.

<p> 
  <img width="1147" height="717" alt="C-Create DC-2-VM" src="https://github.com/user-attachments/assets/28ffae7a-5f0e-4839-b1e2-4bedd41d2468" />
<img width="1032" height="676" alt="D-DC-2 Image Select" src="https://github.com/user-attachments/assets/4dbf4df2-a890-4677-9813-fdbb17ee9985" />

</p>


- Enter appropriate credentials under the "Administrator account" section. Make sure the selected inbound port is set to RDP 3389. Scroll down and check the box to confirm licensing. *Do not save just yet.*

<p> 
  <img width="1192" height="745" alt="C-DC-2 Admin credentials" src="https://github.com/user-attachments/assets/8332aece-12f7-42d6-b7e2-95cc03f7c691" />

</p>


- Under the "Networking" tab, set the virtual network and subnet to the Vnet and subnet previously created. Now you can save/create the domain controller virtual machine.

<p> <img width="1122" height="547" alt="E-DC-2 Networking Vnet Settings" src="https://github.com/user-attachments/assets/26f82643-409a-414f-ab66-f6f31a7f3b86" />

</p>
</p>


- After domain controller VM is created, set the NIC private IP address to "static".
    
<p>
  <img width="1760" height="882" alt="F-DC-2 Networking Settings - Static NIC" src="https://github.com/user-attachments/assets/a1755ee8-bbbc-4965-af64-d05d6da5fc2f" />

</p>


Turn off domain controller's Windows Firewall to test connectivity.
- Go to Remote Desktop Connection to connect to the domain controller VM using its public IP address and administrator account credentials.
- Once logged into the domain controller VM, right click on the Windows "start" button and click "Run".
- Type in "wf.msc" to pull up Windows Firewall. Click on "Windows Defender Properties", set firewall state to "off" under each profile, then apply and save.

<p>
  <img width="992" height="447" alt="H-DC VM Windows FW Off" src="https://github.com/user-attachments/assets/379c7806-9180-49b5-b72f-febc7b5fcb2c" />

</p>

  
  - Second, create the client VM to test the deployment. Assign the same settings for each section as set in the domain controller previously created, except for the "Image" setting. Select "Windows 11, Pro Version 25H2" as the image and select the appropriate size. Assign the same VNet and subnet previously created to the client VM and save/create. 
  
  *Note* In a real world setting, the "Administrator account" credentials will and should be completely different from the credentials set in the domain controller.

<p>
<img width="1557" height="546" alt="J-Create ClientVM Windows 11 Pro" src="https://github.com/user-attachments/assets/9b861d71-8f09-40a0-85e1-42538125d1ce" />
  
</p>

  After client VM is created, set DNS settings to the domain controller's private IP address.
- Copy domain controller's private IP address, go to the client's VM network settings. Click network interface/ipconfiguration, DNS servers, and change to custom DNS servers and enter domain controller's private IP address.

<p> 
  <img width="1345" height="690" alt="K-Client VM DNS Settings" src="https://github.com/user-attachments/assets/d7ca65c8-06e5-4b77-aed3-12995e403e28" />

</p>

- From Azure, restart the client VM. Then, from Remote Desktop, login to client VM and attempt to "ping" the domain controller's private IP address. The output for the DNS settings should show the DC's private IP address. To find this, open PowerShell on the client VM and run ipconfig /all to retrieve info. Ping the IP address to ensure connectivity.

<p>
<img width="1196" height="880" alt="I-ClientVM Ipconfig Test" src="https://github.com/user-attachments/assets/b6691380-f7f6-4521-ba66-532bd3af318c" />

</p>

Step 3: 

Install Active Directory, create a domain admin user within created/designated domain forest, and join client VM to the domain so that created users can access/login to client VM using the created forest domain.

- Login to domain controller VM and install Active Directory Domain Services

<p>
  <img width="1066" height="781" alt="L-Install Active Directory" src="https://github.com/user-attachments/assets/a8b5c507-5949-4780-988e-9eb5312f9f29" />

</p>

- Promote as a "Domain Controller/DC"
  
- Set up a new "forest" as mydomain.com 
- Restart the VM from the Remote Desktop Connection app then log back in to domain controller VM as username: mydomain.com\labuser with the same administrator account password.
  
<p>
  <img width="1130" height="647" alt="M-Promote Server as DC" src="https://github.com/user-attachments/assets/8835267b-51c0-46ff-ab37-0ec3d1541bd9" />

</p>

<p>
<img width="1132" height="641" alt="N-Create New Forest Domain" src="https://github.com/user-attachments/assets/35426eb9-44cd-4d0c-ab4f-3bbf26458e81" />


<B> *Note*: Figure above shows "whateverdomain.com" but should be "mydomain.com" for demo consistency purposes. </B>
</p>

- Create a domain admin user within the domain forest (mydomain.com).
- In Active Directory Users and Computers, create an Organizational Unit (OU) called "_EMPLOYEES".
- Create a new OU called "_ADMINS" 
- Create a new employee Jane Doe with the username "jane_admin" and chosen password. 
- Add user to "Domain Admins" security group
- Always create dedicated administrator account(s) on the domain controller server to access and use for daily task. 

<p>
<img width="777" height="437" alt="P-Add Admin User To Security Grp" src="https://github.com/user-attachments/assets/c0012df8-624c-4cd2-9281-fc75d1536f24" />

</p>


- Close Remote Desktop Connection to domain controller and login with "mydomain.com\jane_admin
<p>

  
  <img width="831" height="582" alt="Q-Login as Jane_Admin" src="https://github.com/user-attachments/assets/73e9bc5d-a53f-4409-ba24-a725236b0e9c" />
  
</p>


- Join client VM to the mydomain.com forest.

- Login to client VM as original local admin (labuser) and join it to the domain.
- How? Right click on start button in client VM, select "System", then "Advanced system settings". 
- Under the "computer name" tab, click "change", add the created forest domain "mydomain.com".
- Confirm changes by entering the administrator "mydomain.com\jane_admin" credentials.


<p> 
<img width="632" height="695" alt="R-Join Client VM to Forest Domain" src="https://github.com/user-attachments/assets/2d85453e-dd87-46f5-a22c-a174e112ed79" />

</p>

- Login to the domain controller as jane_admin and very client VM shows up in Active Directory Users and Computers. 
- Create a new OU named "_CLIENTS" and assign client VM to it.

<p>
<img width="882" height="677" alt="S-Drag client VM to CLIENTS " src="https://github.com/user-attachments/assets/449d67e6-2c05-4090-9adb-5adb54002d90" />

</p>

Step 4: Create Users and set up client VM for non-admin users (Remote Desktop for demo purposes)

- Log into client VM as mydomain.com\jane_admin (authz'd admin).
- Configure system settings to allow selected domain groups access to remote desktop/client VM.
- This allows authorized users within a given group to access a selected VM. 

<p>
  <img width="1007" height="775" alt="T-RemoteDesktop for Non-Admin on ClientVM" src="https://github.com/user-attachments/assets/1b94faed-95c1-4861-b823-234ed73b2fe7" />

</p>

<B> *Note*: Typically, this would be further filtered/controlled by Group Policy but consider the steps for demo purposes. </B>
<p>

- Create additional users to have access to VM using PowerShell from the domain controller.
- Login to the domain controller as authz'd admin (jane_admin).
- Open Windows PowerShell ISE and run as administrator.
- Create a new file, then paste and run given script into it (will include initial user passwords).
- Open ADUC to observe created accounts in the appropriate OU folder.
</p>

<p>
<img width="1001" height="542" alt="U-Observe User Creation" src="https://github.com/user-attachments/assets/969ef132-8056-4e57-9f22-4e99152435d2" />
  
</p>

- Test login into client VM with any given user to affirm deployment.

<p>
<img width="1115" height="605" alt="V-Test New User Login" src="https://github.com/user-attachments/assets/8332abcc-42e3-43a9-ba49-8379456a96ef" />

</p>
<br />

