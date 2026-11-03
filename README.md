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

<p>
Phase 1: Prepare the Azure Infrastructure

Step 1:  Create a Resource Group.

- Within “Search”, type “Resource Group”
- Select “Create”
  - Resource Group*:  Active-Directory
  - Region*:  (US) East US (or whatever region you want)
- Select “Review & Create”, then “Create”
<img width="975" height="493" alt="image" src="https://github.com/user-attachments/assets/5256300e-bcff-4261-8bfb-8dfdd06f8e41" />

Step 2:  Create a Virtual Network and Subnet.

- Go to “search” again and type “Virtual Network”
- Select “Create”
  - Resource Group*:  Active-Directory
  - Virtual network name*:  Active-Directory-Vnet
<img width="975" height="495" alt="image" src="https://github.com/user-attachments/assets/54fe2d77-3445-46c3-b846-01b0f2d395fc" />



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

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
