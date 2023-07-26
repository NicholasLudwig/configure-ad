<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This repository outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create resources using Azure Virtual Machines
- Install Active Directory on Domain Controller
- Create an Administrator using Active Directory
- Connect client to the domain and setup Remote Desktop for non-admins
- Use Powershell to create additional users to test Active Directory configuration

<h2>Deployment and Configuration Steps</h2>

<p>
Create 2 virtual machines using Azure, one will be named DC-1 (for the domain controller) and one will be named Client-1. Ensure that the DC-1's NIC Private IP is changed to Static by navigating to DC-1 > Networking > click on Network Interface > IP Configurations > ipconfig1. Ensure that both DC-1 and Client-1 are under the same vnet by using Network Watcher. Ensure that DC-1 and Client-1 are connected by logging into DC-1 and enabling ICMPv4 using Windows Firewall. Test the connection by logging into Client-1 and pinging DC-1's <b>private</b> IP address. 
</p>
<br />

![ad-vm-setup](https://github.com/NicholasLudwig/configure-ad/assets/104456331/047a77a2-9136-4917-a0a1-8ccc9eb5f057)

<br />
<p>
Install Active Directory Domain Services on DC-1 by opening up Windows Server Manager and selecting Add Roles and Features. Under Server Roles select Active Directory Domain Services and click Add Features. Once Active Directory Domain Services has been installed, click the "warning" flag in the upper righthand navigation pane and click promote server to domain controller. This will allow us to specify our own domain. Select Add a new forest and specify the domain name. Finish installing and then restart and reconnect to DC-1.
</p>
<br />

![ad-dc-setup](https://github.com/NicholasLudwig/configure-ad/assets/104456331/301aea76-1665-4c79-b9e8-b65f78e019ff)

<p>
Once the restart is complete navigate to Tools > Active Directory Users and Computers. Expand the mydomain.com tree > right click > New > Organizational Units. Create two new OUs, one for Employees and one for Admins. Under the new OU Admins, right click > New > User and create the admin using an appropriate name and password. Be sure to check the box for password never expires. Add the new admin to the Domain Admins group by right clicking the user > Add to group. Enter the object names (Domain Admins), click Check Names then click Ok. Log out and restart DC-1, then log back in as the new administrator.
</p>
<br />

![ad-admin-setup](https://github.com/NicholasLudwig/configure-ad/assets/104456331/cf107a2b-d465-4dfe-bbb3-a89a0fb3d236)

<br />
<p>
To connect Client-1 to the Domain Controller, begin by setting Client-1's DNS settings to DC-1's <b>private</b> IP address. Do this in the Azure portal by navigating to Client-1 > Networking > NIC > DNS servers > Custom. Login to Client-1 as the original local admin and open system settings. Click Rename this PC (advanced) > Change > Member of Domain and enter your domain name. Click Ok and enter the credentials of someone with access to the domain (such as the new administrator previously created). Verify Client-1 has joined the domain by checking Active Directory Users and Computers > Computers on DC-1.
</p>
<br />

![join-client1-domain](https://github.com/NicholasLudwig/configure-ad/assets/104456331/c5b46c51-5646-4b0d-968f-2e3b0d0d4057)

<br />
<p>
Allow non-administrator users access via Remote Desktop by logging into Client-1 as the administrator created previously. Open system properties > Remote Desktop > Select users that can remotely access this PC. Click Add and enter the object names Domain Users. Click Check Names then Ok. Normally, this would be done with a Group Policy to make many changes at once. Create additional users to test access by opening Powershell as an administrator on DC-1. Run the script to create users and observe they are created by checking the appropriate Organizational Unit in Active Directory Users and Computers. Log out of Client-1 and attempt to log back in with one of the new users just created to test the configuration(s). Successful login indicates the correct configuration. Verify by running command prompt and entering "whoami" and "hostname".
</p>
<br />

![test-new-user](https://github.com/NicholasLudwig/configure-ad/assets/104456331/405432d9-3651-44b6-947c-448a3d83ff7e)
