<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create two Virtual Machines within the same Virtual Network (VNET): a DNS Server (Domain Controller) and a Client.
- Verify connectivity between the Client and the Domain Controller.
- Install Active Directory on the Domain Controller and configure a new forest named mydomain.com.
- In Active Directory, create an Admin user and a Standard user.
- Join the Client VM to the mydomain.com domain.
- Enable Remote Desktop access for non-administrative users on the Client VM.
- Add 1,000 additional users in Active Directory and log into the Client VM with one of these new users.

![image](https://github.com/user-attachments/assets/4365bd14-11cd-4459-b29a-bd912602f4ad)

<h2>Deployment and Configuration Steps</h2>

In this lab, we will set up two Virtual Machines (VMs) within the same Virtual Network (VNET): one as a Domain Controller (DC) and the other as a Client machine. The DC will be assigned a static IP address to provide Active Directory services to the Client. The Client machine will be joined to the domain, and its DNS settings will be configured to use the DC as its DNS server.

![image](https://github.com/user-attachments/assets/2717fd34-9616-4940-a0e6-f1185969dfb2)

DC-1 must be assigned a static private IP address. To verify connectivity, we will attempt to ping DC-1 from Client-1. Initially, the ping will fail. To resolve this, we need to enable ICMPv4 traffic through the firewall on DC-1. Once this is done, the ping from Client-1 to DC-1 should succeed.

![image](https://github.com/user-attachments/assets/f8079210-04cc-4b07-a4b0-846ab62ef425)

![image](https://github.com/user-attachments/assets/e58d1520-9cab-4a9b-b9e2-20bfa7cc2585)

Next, we'll log back into DC-1 to install Active Directory Users and Computers. Promote the VM to a Domain Controller, create a new forest named mydomain.com, and restart the VM. After the restart, log back into DC-1 using the credentials mydomain.com\labuser. If the steps were followed correctly, you should be able to run Active Directory Users and Computers as demonstrated below.

![image](https://github.com/user-attachments/assets/bbd5c0d4-8a68-4400-ac0d-f3165d0d7c0a)

To begin, we will create Organizational Units (OUs):
- Create an OU named _EMPLOYEES and another named _ADMINS:
  - Right-click on the domain area.
  - Select New > Organizational Unit and provide the name.
To add a new user to an OU:
- Open your desired OU, right-click inside it, and select New > User.
- Fill in the user details:
  - Name: Jane Doe
  - Username: Jane_admin (as she will be an Admin).
Assign Jane Doe to the Domain Admins security group to grant her admin privileges.

![image](https://github.com/user-attachments/assets/c9342079-1a6f-4b4b-bbd7-b20e6f0a2ebb)
![image](https://github.com/user-attachments/assets/6631e065-764f-4f39-83e6-7f33d6701712)


You can now use Jane_admin as the administrator account. Next, we will join Client-1 to the domain (mydomain.com). To do this:
 -  In the Azure portal, update Client-1's DNS settings to point to the Domain Controller's (DC) private IP address.
 -  Restart Client-1 through the Azure portal.
   
The screenshot below confirms that Client-1 is using DC-1's DNS.

![image](https://github.com/user-attachments/assets/ff9a0496-c417-4078-be46-af4836eb0270)
![image](https://github.com/user-attachments/assets/a0a2dc16-0022-45b5-9578-8bae08bcca19)


To join Client-1 to the domain:

  - Open System Settings and navigate to the About section.
  - On the right-hand side, click Rename this PC (Advanced).
  - Select the option to change the domain and enter mydomain.com.
  - Provide your credentials in the format mydomain.com\labuser.
  - Restart the computer. Once it restarts, Client-1 will be part of mydomain.com.

![image](https://github.com/user-attachments/assets/b9c657a1-952c-4fbf-8dd9-9757355fe7d6)

Client-1 has successfully joined the domain. Next, we’ll configure Remote Desktop for non-administrative users on Client-1.
  - Log into Client-1 using an administrator account.
  - Open System Properties and navigate to the Remote Desktop tab.
  - Allow domain users to access Remote Desktop.
Once these steps are completed, you should be able to log into Client-1 using a standard user account.

![image](https://github.com/user-attachments/assets/882097cc-68df-43e9-bcfc-599ce9ca2f80)

Finally, to confirm that standard users can access Client-1 via Remote Desktop, we will use a PowerShell script to generate thousands of users in the domain. Once the users are created, we will select one and attempt to log in to Client-1 using Remote Desktop.

![image](https://github.com/user-attachments/assets/65f22d4f-08ae-49d5-9d27-97207e6e5d85)
![image](https://github.com/user-attachments/assets/b5275151-8b29-40ec-aa65-07e6ea1a3933)

The PowerShell script successfully created a user with the username "bab.hubo". Using these credentials, we logged into Client-1 as a standard user.
