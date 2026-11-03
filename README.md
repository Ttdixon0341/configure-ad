 
On-premises Active Directory Deployed in the Cloud (Azure)
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.
Environments and Technologies Used
•	Microsoft Azure (Virtual Machines/Computer)
•	Remote Desktop
•	Active Directory Domain Services
•	PowerShell
Operating Systems Used
•	Windows Server 2022 Datacenter 
•	Windows 11 Pro (26H2)
High-Level Deployment and Configuration Steps
•	Phase 1: Prepare the Azure Infrastructure
o	Create an Azure Resource Group
o	Create a Virtual Network and Subnet
o	Create the Domain Controller Virtual Machine
o	Create the Client Virtual Machine
•	Phase 2: Configure Network Connectivity
o	Assign a Static Private IP Address to the Domain Controller
o	Temporarily Disable Windows Firewall on the Domain Controller
o	Configure Client-1 to Use the Domain Controller as Its DNS Server
•	Phase 3: Deploy and Configure Active Directory
o	Install Active Directory Domain Services (AD DS)
o	Promote the Server to a Domain Controller
o	Create Organizational Units (OUs)
o	Create a Domain Administrator Account
•	Phase 4: Join Client-1 to the Domain
o	Join Client-1 to the Active Directory Domain
o	Configure Remote Desktop Access for Domain Users
•	Phase 5: Create and Manage Domain User Accounts
o	Create Multiple Domain User Accounts Using PowerShell
o	Verify User Account Creation

Deployment and Configuration Steps

Phase 1: Prepare the Azure Infrastructure
Step 1:  Create a Resource Group.
•	Within “Search”, type “Resource Group”
•	Select “Create”
o	Resource Group*:  Active-Directory
o	Region*:  (US) East US (or whatever region you want)
•	Select “Review & Create”, then “Create”
 
Step 2:  Create a Virtual Network and Subnet.
•	Go to “search” again and type “Virtual Network”
•	Select “Create”
o	Resource Group*:  Active-Directory
o	Virtual network name*:  Active-Directory-Vnet
 
•	Select “Review & Create”, then “Create”
Step 3:  Create the Domain Controller VM
•	Go to “search” and type “Virtual Machines”
o	Resource group*:  Active-Directory
o	Virtual machine name*:  Domain-Controller
o	Region*:  (US) East US (must be the same Region as your virtual network)
o	Image*:  Windows Server 2022 Datacenter: Azure Edition Hotpatch
	(you might have to change the Size* choose anything with 2 vcpus)
o	Select a Username and Password
o	Select the check boxes at the bottom, then select “Next” TWICE (going to Networking tab)
o	Virtual Network*:  Active-Directory-Vnet
o	Subnet*:  default – 10.0.0.0/24
o	Select “Review & Create”, then “Create”
Step 4:  Create the Client Virtual Machine
•	Go to “search” and type “Virtual Machines”
o	Resource group*:  Active-Directory
o	Virtual machine name*:  Client-1
o	Region*:  (US) East US (must be the same Region as your virtual network)
o	Image*:  Windows 11 Pro, version 26H2
	(you might have to change the Size* choose anything with 2 vcpus)
o	Select a Username and Password
o	Select the check boxes at the bottom, then select “Next” TWICE (going to Networking tab)
o	Virtual Network*:  Active-Directory-Vnet
o	Subnet*:  default – 10.0.0.0/24
o	Select “Review & Create”, then “Create”
Phase 2: Configure Network Connectivity 
Step 1: Assign a Static Private IP Address to the Domain Controller
The Domain Controller currently uses a dynamic IP address, meaning its IP address could change whenever the VM is restarted or redeployed. We want to prevent this by assigning it a static IP address, so it remains consistent.  Since the Domain Controller will also be functioning as a DNS server, we will configure Client-1 to use the Domain Controller's static IP address as its DNS server. This will allow Client-1 to properly resolve domain names and communicate with the domain.
•	Go to “search” and type “Virtual Machines”
•	Open Domain Controller VM
o	In the left column select Networking/Network Settings
 
•	Click on the vNIC (arrow is pointing to it)
•	At the bottom of the screen click on “ipconfig1”
•	Under Private IP address settings, select “Static”, click “Save”
Step 2: Temporarily Disable Windows Firewall on the Domain Controller
We will temporarily disable Windows Firewall on the virtual machine to simplify network connectivity testing within the lab environment. This will help eliminate firewall restrictions as a potential cause of connectivity issues while testing communication between the virtual machines.
Note: This is strictly for lab testing purposes.  NEVER do this in real life.
•	Remotely log-in to Domain Controller VM via public IP address via Remove Desktop
•	Right-click the Start Menu, select ‘Run’ and type “wf.msc”
•	Click on “Windows Defender Firewall Properties”
o	Firewall state:  OFF
o	Select “Apply” then “OK”
Step 3: Configure Client-1 to Use the Domain Controller as Its DNS Server
Since the Domain Controller is also functioning as a DNS server, Client-1 needs to use it to resolve domain names and locate network resources. This allows Client-1 to communicate with the Domain Controller, find the Active Directory domain, and successfully join the domain.

•	Obtain Domain-Controller private IP address
•	Open Client-1 VM
o	In the left column select Networking/Network Settings
o	Click on the vNIC (like we did before with Domain-Controller)
o	In the left column click on “DNS servers”
o	Click “custom”
o	Within ‘Custom DNS servers’ put in the Domain-Controller private IP address
•	Restart Client-1 VM (this allows all changes to take effect)
•	Remotely log into to Client-1 VM
•	Open Powershell and ping Domain-Controller
o	Type:  ping <Domain-Controller private ip address>
 
•	Now type:  ipconfig /all
o	At the bottom it should read ‘DNS Servers. . . . .’ and show the Domain-Controller private IP address (confirming it has been changed)
 
Phase 3: Deploy and Configure Active Directory
Step 1: Install Active Directory Domain Services (AD DS)
•	RDP into Domain-Controller
•	Go to Search, type “Server Manager”
•	Click on “Add Roles and Features”
•	Keep clicking ‘Next’ until you come to Select Server Role
•	Select ‘Active Directory Domain Services’, select ‘Add Feaure’
 

•	Keep selecting ‘Next’, then ‘Install’
Step 2: Promote the Server to a Domain Controller
•	Return to Server Manager.  In the upper-right corner will be a flag.  Select it.  Click on “Promote this server to a domain controller”
 

•	Click “Add a new forest”
•	Root domain name:  mydomain.com
•	Keep clicking next, then Install.
•	Restart Domain-Controller VM
•	Now that the AD is a DC, we have to verify that we want to log in as a domain user.
o	Username:  mydomain.com\<your user name>
Step 3: Create Organizational Units (OUs)
You will create a Domain Admin within the domain
•	Return to Domain-Controller VMm
•	Go to Start/Windows Administrative Tools/Active Directory Users and Computers
•	Right click on mydomain.com/New/Organizational Unit
 

•	Name:  _EMPLOYEES
•	Create a second Organizational Unit (OU) and name it:  _ADMINS
Step 4: Create a Domain Administrator Account
•	Right-click _ADMINS/New/User
o	Follow the prompts to create the user
•	Right-click on the new users account, select Properties
o	Select ‘Member of’ tab
o	Click ‘Add’
o	Inside ‘Enter the object names to select’ type in “domain admins”
o	Click ‘Check Names’, click OK.
•	Log out of Domain-Controller VM and log back in with your admin user.
o	mydomain.com\<admin user>

Phase 4: Join Client-1 to the Domain
Joining Client-1 to the domain allows domain user accounts to authenticate and access the machine. This means authorized domain users can log in to Client-1 remotely using Remote Desktop Protocol (RDP), provided they have the appropriate permissions. It also allows user accounts and access policies to be centrally managed through Active Directory.
Step 1: Join Client-1 to the Active Directory Domain
•	Log into Client-1 VM
•	Right-click Start/System
•	Scroll down and select ‘Domain or Workgroup’
•	Under ‘Computer Name’ tab click ‘Change’
•	Select ‘Domain’ and change the name to ‘mydomain.com’, hit OK
o	Because only an admin users can make these changes, use your new admin user to authorize these changes.
Step 2: Configure Remote Desktop Access for Domain Users
•	Log into Client-1 as your admin user
•	Right-click Start/System
•	Scroll down, select ‘Remove Desktop’, then ‘Desktop Users’
•	Click ‘Add’.  type: “domain users”. Click ‘Check Names’, click OK
(Normally you’d want to do this with Group Policy that allows you to change many systems at once)

Phase 5: Create and Manage Domain User Accounts
Step 1: Create Multiple Domain User Accounts Using PowerShell
•	Log into Domain-Controller as a user admin
•	Open Powershell ISE as an administrator
•	Paste Script into Powershell ISE and run the script
 

Step 2: Verify User Account Creation
•	Return to Active Directory Users and Computers/mydomain.com/_EMPLOYEES and see all the additional users that have been added
o	Username:  <username>
o	Password:  Password1 (all added users from the script will have this password)

At the end of this deployment, we will have:
•	A functioning Windows Server 2022 Domain Controller hosted in Azure.
•	Active Directory Domain Services installed and configured.
•	A new Active Directory forest and domain (mydomain.com).
•	A functioning DNS server integrated with Active Directory.
•	A Windows 11 client machine successfully joined to the domain.
•	Separate organizational units for employees and administrators.
•	A dedicated Domain Administrator account.
•	Multiple standard domain user accounts created through PowerShell.
•	Remote Desktop access configured for standard domain users.
The completed environment will demonstrate how an organization can use Active Directory to centrally manage user accounts, authenticate users, organize resources, and control access to computers across a network.

