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

- Select “Review & Create”, then “Create”

Step 3:  Create the Domain Controller VM

- Go to “search” and type “Virtual Machines”
  - Resource group*:  Active-Directory
  - Virtual machine name*:  Domain-Controller
  - Region*:  (US) East US (must be the same Region as your virtual network)
  - Image*:  Windows Server 2022 Datacenter: Azure Edition Hotpatch
    - (you might have to change the Size* choose anything with 2 vcpus)
  - Select a Username and Password
  - Select the check boxes at the bottom, then select “Next” TWICE (going to Networking tab)
  - Virtual Network*:  Active-Directory-Vnet
  - Subnet*:  default – 10.0.0.0/24
  - Select “Review & Create”, then “Create”

Step 4:  Create the Client Virtual Machine

- Go to “search” and type “Virtual Machines”
  - Resource group*:  Active-Directory
  - Virtual machine name*:  Client-1
  - Region*:  (US) East US (must be the same Region as your virtual network)
  - Image*:  Windows 11 Pro, version 26H2
    - (you might have to change the Size* choose anything with 2 vcpus)
  - Select a Username and Password
  - Select the check boxes at the bottom, then select “Next” TWICE (going to Networking tab)
  - Virtual Network*:  Active-Directory-Vnet
  - Subnet*:  default – 10.0.0.0/24
  - Select “Review & Create”, then “Create”

<h2>Phase 2: Configure Network Connectivity </h2>

Step 1: Assign a Static Private IP Address to the Domain Controller

The Domain Controller currently uses a dynamic IP address, meaning its IP address could change whenever the VM is restarted or redeployed. We want to prevent this by assigning it a static IP address, so it remains consistent.  Since the Domain Controller will also be functioning as a DNS server, we will configure Client-1 to use the Domain Controller's static IP address as its DNS server. This will allow Client-1 to properly resolve domain names and communicate with the domain.

- Go to “search” and type “Virtual Machines”
- Open Domain Controller VM
  - In the left column select Networking/Network Settings

<img width="1920" height="866" alt="Screenshot 2026-10-29 153209" src="https://github.com/user-attachments/assets/e0bc7063-70b7-49df-be92-9fbda70052b2" />

- Click on the vNIC (arrow is pointing to it)
- At the bottom of the screen click on “ipconfig1”
- Under Private IP address settings, select “Static”, click “Save”

Step 2: Temporarily Disable Windows Firewall on the Domain Controller

We will temporarily disable Windows Firewall on the virtual machine to simplify network connectivity testing within the lab environment. This will help eliminate firewall restrictions as a potential cause of connectivity issues while testing communication between the virtual machines.
Note: This is strictly for lab testing purposes.  NEVER do this in real life.

- Remotely log-in to Domain Controller VM via public IP address via Remove Desktop
- Right-click the Start Menu, select ‘Run’ and type “wf.msc”
- Click on “Windows Defender Firewall Properties”
  - Firewall state:  OFF
  - Select “Apply” then “OK”
