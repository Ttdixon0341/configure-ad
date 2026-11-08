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

- Windows Server 2022 Datacenter
- Windows 11 Pro (26H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Phase 1: Prepare the Azure Infrastructure
  - Create an Azure Resource Group
  - Create a Virtual Network and Subnet
  - Create the Domain Controller Virtual Machine
  - Create the Client Virtual Machine
- Phase 2: Configure Network Connectivity
  - Assign a Static Private IP Address to the Domain Controller
  - Temporarily Disable Windows Firewall on the Domain Controller
  - Configure Client-1 to Use the Domain Controller as Its DNS Server
- Phase 3: Deploy and Configure Active Directory
  - Install Active Directory Domain Services (AD DS)
  - Promote the Server to a Domain Controller
  - Create Organizational Units (OUs)
  - Create a Domain Administrator Account
- Phase 4: Join Client-1 to the Domain
  - Join Client-1 to the Active Directory Domain
  - Configure Remote Desktop Access for Domain Users
- Phase 5: Create and Manage Domain User Accounts
  - Create Multiple Domain User Accounts Using PowerShell
  - Verify User Account Creation

<h2>Deployment and Configuration Steps</h2>

<h2>Phase 1: Prepare the Azure Infrastructure</h2>

Step 1:  Create an Azure Resource Group.

- Log into Microsoft Azure
- Within “Search”, type “Resource Group”
- Select “Create”
- Resource Group*:  Active-Directory
- Region*:  (US) East US (or whatever region you want)
- Select “Review & Create”, then “Create”
<br><br>
<img width="986" height="858" alt="Screenshot 2026-11-08 082337" src="https://github.com/user-attachments/assets/c059a9d6-8533-4627-8fe7-31f6d2581684" />
<br><br>
Step 2:  Create a Virtual Network and Subnet.
<br><br>

- Go to “search” again and type “Virtual Network”
- Select “Create”
- Resource Group*:  Active-Directory
- Virtual network name*:  Active-Directory-Vnet
- Select “Review & Create”, then “Create”
<br><br>
<img width="1031" height="925" alt="Screenshot 2026-11-08 083022" src="https://github.com/user-attachments/assets/d8e233e5-2c64-467e-9133-6d5375777f06" />
<br><br>
Step 3:  Create the Domain Controller Virtual Machine
<br><br>

- Go to “search” and type “Virtual Machines”
- Click "+Create" and select "Virtual Machine"
- Resource group*:  Active-Directory
- Virtual machine name*:  DC-1
- Region*:  (US) East US (must be the same Region as your virtual network)
- Image*:  Windows Server 2022 Datacenter: Azure Edition Hotpatch
<br><br>
<img width="1020" height="877" alt="Screenshot 2026-11-08 084126" src="https://github.com/user-attachments/assets/07e95f09-0a50-4b5b-9f2f-3b546176a55b" />
<br><br>

- Under Size* select "See all sizes".  Choose anything with 2 vCPUs or more and at least 8 GiB of RAM.
- Username: labuser (or whatever you want to use)
- Password: Cyberlab1234 (or whatever you want to use)
<br><br>
<img width="1043" height="876" alt="Screenshot 2026-11-08 085443" src="https://github.com/user-attachments/assets/b225439b-7326-4e8f-b7ea-dd194f3ddf39" />
<br><br>

- Select the check boxes at the bottom, then select “Next” TWICE (going to Networking tab)
- Virtual Network*:  Active-Directory-Vnet
- Subnet*:  default – 10.0.0.0/24
- Select “Review & Create”, then “Create”
<br><br>
<img width="1017" height="925" alt="Screenshot 2026-11-08 090733" src="https://github.com/user-attachments/assets/dd699a4c-2d45-4f66-8121-9443d139db0f" />
<br><br>

Step 4:  Create the Client Virtual Machine

- Go to “search” and type “Virtual Machines”
- Click "+Create" and select "Virtual Machine"
- Resource group*:  Active-Directory
- Virtual machine name*:  Client-1
- Region*:  (US) East US (must be the same Region as your virtual network)
- Image*:  Windows 11 Pro, version 26H2
<br><br>
<img width="1043" height="872" alt="Screenshot 2026-11-08 091554" src="https://github.com/user-attachments/assets/3a5951f3-9aea-41c3-8df4-b3d78129b5e2" />
<br><br>

- Under Size* select "See all sizes".  Choose anything with 2 vCPUs or more and at least 8 GiB of RAM.
- Username: labuser (or whatever you want to use)
- Password: Cyberlab1234 (or whatever you want to use)
<br><br>
<img width="1052" height="870" alt="Screenshot 2026-11-08 092300" src="https://github.com/user-attachments/assets/c6b36c34-a275-4929-be56-885695b82ea9" />
<br><br>

- Select the check boxes at the bottom, then select “Next” TWICE (going to Networking tab)
- Virtual Network*:  Active-Directory-Vnet
- Subnet*:  default – 10.0.0.0/24
- Select “Review & Create”, then “Create”
<br><br>
<img width="1031" height="926" alt="Screenshot 2026-11-08 092525" src="https://github.com/user-attachments/assets/34770c19-7863-41da-a599-1eee25eb8d60" />
<br><br>

<h2>Phase 2: Configure Network Connectivity </h2>

Step 1: Assign a Static Private IP Address to the Domain Controller

The Domain Controller currently uses a dynamic IP address, meaning its IP address could change whenever the VM is restarted or redeployed. We want to prevent this by assigning it a static IP address, so it remains consistent.  Since the Domain Controller will also be functioning as a DNS server, we will configure Client-1 to use the Domain Controller's static IP address as its DNS server. This will allow Client-1 to properly resolve domain names and communicate with the domain.

- Go to “search” and type “Virtual Machines”
- Open DC-1 by clicking on the name
- In the left column select Networking/Network Settings
- Click on the vNIC (arrow is pointing to it)
<br><br>
<img width="1465" height="927" alt="Screenshot 2026-11-08 093324" src="https://github.com/user-attachments/assets/320e7695-9920-42b7-85fa-5c39001253ac" />
<br><br>

- At the bottom of the screen click on “ipconfig1”
- Under Private IP address settings, select “Static”, click “Save”
<br><br>
<img width="1860" height="927" alt="Screenshot 2026-11-08 093632" src="https://github.com/user-attachments/assets/fbfae0f4-9434-42e7-b1c1-7ef096e14019" />
<br><br>

Step 2: Temporarily Disable Windows Firewall on the Domain Controller

We will temporarily disable Windows Firewall on the virtual machine to simplify network connectivity testing within the lab environment. This will help eliminate firewall restrictions as a potential cause of connectivity issues while testing communication between the virtual machines.
Note: This is strictly for lab testing purposes.  NEVER do this in real life.

- Remotely log-in to Domain Controller VM via public IP address via Remove Desktop
- Right-click the Start Menu, select ‘Run’ and type “wf.msc”
- Click on “Windows Defender Firewall Properties”
  - Firewall state:  OFF
  - Select “Apply” then “OK”

Step 3: Configure Client-1 to Use the Domain Controller as Its DNS Server

Since the Domain Controller is also functioning as a DNS server, Client-1 needs to use it to resolve domain names and locate network resources. This allows Client-1 to communicate with the Domain Controller, find the Active Directory domain, and successfully join the domain.

- Obtain Domain-Controller private IP address
- Open Client-1 VM
- In the left column select Networking/Network Settings
- Click on the vNIC (like we did before with Domain-Controller)
- In the left column click on “DNS servers”
- Click “custom”
- Within ‘Custom DNS servers’ put in the Domain-Controller private IP address
- Restart Client-1 VM (this allows all changes to take effect)
- Remotely log into to Client-1 VM
- Open Powershell and type:  ping <Domain-Controller private ip address> to the ping Domain-Controller


<img width="975" height="548" alt="image" src="https://github.com/user-attachments/assets/f79933cb-b752-4f6f-b26f-2165c8e58e94" />

- Now type:  ipconfig /all
- At the bottom it should read ‘DNS Servers. . . . .’ and show the Domain-Controller private IP address (confirming it has been changed)

<img width="1920" height="1080" alt="Screenshot 2026-10-30 081830" src="https://github.com/user-attachments/assets/968012cb-2a77-490b-9601-5f073086e018" />

<h2>Phase 3: Deploy and Configure Active Directory</h2>

Step 1: Install Active Directory Domain Services (AD DS)

- RDP into Domain-Controller
- Go to Search, type “Server Manager”
- Click on “Add Roles and Features”
- Keep clicking ‘Next’ until you come to Select Server Role
- Select ‘Active Directory Domain Services’, select ‘Add Feaure’

<img width="975" height="547" alt="image" src="https://github.com/user-attachments/assets/429a198d-a941-4954-95ab-7594ca52fc56" />

- Keep selecting ‘Next’, then ‘Install’

Step 2: Promote the Server to a Domain Controller

- Return to Server Manager.  In the upper-right corner will be a flag.  Select it.  Click on “Promote this server to a domain controller”

<img width="975" height="548" alt="image" src="https://github.com/user-attachments/assets/ef70dafa-7822-43e7-901c-d2e960d12b45" />

- Click “Add a new forest"
- Root domain name:  mydomain.com
- Keep clicking next, then Install.
- Restart Domain-Controller VM
- Now that the AD is a DC, we have to verify that we want to log in as a domain user.
  - Username:  mydomain.com\<your user name>

Step 3: Create Organizational Units (OUs)

You will create a Domain Admin within the domain

- Return to Domain-Controller VMm
- Go to Start/Windows Administrative Tools/Active Directory Users and Computers
- Right click on mydomain.com/New/Organizational Unit

<img width="975" height="548" alt="image" src="https://github.com/user-attachments/assets/048afeef-7ceb-47ed-8046-19644ef68e01" />

- Name:  _EMPLOYEES
- Create a second Organizational Unit (OU) and name it:  _ADMINS

Step 4: Create a Domain Administrator Account

- Right-click _ADMINS/New/User and follow the prompts to create the user
- Right-click on the new users account, select Properties
- Select ‘Member of’ tab
- Click ‘Add’
- Inside ‘Enter the object names to select’ type in “domain admins”
- Click ‘Check Names’, click OK.
- Log out of Domain-Controller VM and log back in with your admin user.
  - mydomain.com\<admin user>

<h2>Phase 4: Join Client-1 to the Domain</h2>

Joining Client-1 to the domain allows domain user accounts to authenticate and access the machine. This means authorized domain users can log in to Client-1 remotely using Remote Desktop Protocol (RDP), provided they have the appropriate permissions. It also allows user accounts and access policies to be centrally managed through Active Directory.

Step 1: Join Client-1 to the Active Directory Domain

- Log into Client-1 VM
- Right-click Start/System
- Scroll down and select ‘Domain or Workgroup’
- Under ‘Computer Name’ tab click ‘Change’
- Select ‘Domain’ and change the name to ‘mydomain.com’, hit OK

(Because only an admin users can make these changes, use your new admin user to authorize these changes)

Step 2: Configure Remote Desktop Access for Domain Users

- Log into Client-1 as your admin user
- Right-click Start/System
- Scroll down, select ‘Remove Desktop’, then ‘Desktop Users’
- Click ‘Add’.  type: “domain users”. Click ‘Check Names’, click OK

(Normally you’d want to do this with Group Policy that allows you to change many systems at once)

<h2>Phase 5: Create and Manage Domain User Accounts</h2>

Step 1: Create Multiple Domain User Accounts Using PowerShell

- Log into Domain-Controller as a user admin
- Open Powershell ISE as an administrator
- Paste Script into Powershell ISE and run the script

<img width="975" height="548" alt="image" src="https://github.com/user-attachments/assets/13805173-ce59-40e4-a4d6-c4cdf144e9e2" />

Step 2: Verify User Account Creation

- Return to Active Directory Users and Computers/mydomain.com/_EMPLOYEES and see all the additional users that have been added
  - Username:  <username>
  - Password:  Password1 (all added users from the script will have this password)
