# configure-ad
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

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setting up our resources in Azure.
- Ensuring connectivity between the Client and Domain Controller.
- Installing Active Directory.
- Creating an Admin and Normal User Account within AD.
- Joining a Client to our Domain.
- Setting up Remote Desktop for non-administrative users on the Client
- Creating 10,000 users and logging in with one of them.

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://imgur.com/Q7tS127.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
First, create two VMs in Azure. One should be using Windows 10 and the other should be using Windows Server. Make sure they're both in the same RG and using the same Vnet. Check the network topology in Network Watcher to make sure everything looks right (refer to the picture above). After all of that, we're going to change our Domain Controller's (Server 2022) private IP address to static. In Azure, navigate to Virtual Machines -> DC-1 -> Networking -> Network Interface -> IP Configurations -> Private IP Address -> Change to Static.
</p>
<br />

<p>
<img src="https://imgur.com/QS7Et6e.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we're going to establish and verify a connection between Client-1 and DC-1. Log into Client-1 and ping DC-1's private IP -t (perpetual ping). Now, log into DC-1 and enable ICMPv4 on the local Windows Firewall. Check back with Client-1 to verify that the ping is now getting a response.
</p>
<br />

<p>
<img src="https://imgur.com/oOoNK0u.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now we'll install Active Directory on DC-1. Go into the Server Manager and click "Add Roles and Features". Click through the first few prompts, and then select Active Directory Domain Services. Click through the rest of the prompts and then click Install. Once AD is done installing, click on the flag icon in the top right, then click on "Promote this server to a domain controller". Select the option to create a new forest. Use mydomain.com. Click through the prompts and click Install at the end. Sign back into DC-1 as mydomain.com\labuser (or mydomain.com\whatever your username is).
</p>
<br />

<p>
<img src="https://imgur.com/Ll4NiIj.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once you're back into DC-1, open Active Directory Users and Computers (ADUC) by typing it in the search bar. Then, expand mydomain.com, right click -> New -> Organizational Unit and call it "_EMPLOYEES". Do this again for an OU called "_ADMINS". In _ADMINS, create a new user by right clicking -> New -> User. Make Jane Doe, and make her username "jane_admin". Now add her to the Domain Admins Security Group by right clicking Jane Doe -> Properties -> Member Of -> Add -> type "Domain Admins" -> Check Names -> OK -> Apply -> OK. Now log out of DC-1 and log back in as Jane, we'll be using that account from now on.
</p>
<br />

<p>
<img src="https://imgur.com/fNYrVrf.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now we'll join Client-1 to our domain. Navigate to the Azure Portal, click on Client-1 -> Networking -> Network Interface -> DNS Servers -> Custom -> input DC-1's private IP -> Save -> Virtual Machines -> Client-1 -> Restart. Now Client-1 should restart, when it does, sign back in. Now right click Start -> click System -> Rename this PC (advanced) -> Change -> Domain -> type in "mydomain.com" -> OK -> log in using Jane Doe's information -> click OK on the pop-up. Client-1 should restart. 
</p>
<br />

<p>
<img src="https://imgur.com/1CkguzC.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We need to make sure that anyone can Remote Desktop in to Client-1. Log back in to Client-1 as Jane Doe. Now right click Start -> click on System -> Remote Desktop -> Select users that can remotely access this PC -> Add -> "domain users" -> Check names -> OK -> OK. Usually, this would be done with Group Policy to make things faster. Now, go back into DC-1 and access ADUC. Expand mydomain.com and access the Computers container. Verify that Client-1 is there. 
</p>
<br />

<p>
<img src="https://imgur.com/1R1dlNb.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Finally, we'll be using a PowerShell script to add 10,000 users. Go back into DC-1 and run PowerShell ISE as an Administrator. Copy the raw contents from <a href="https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1">this script</a> and paste them into a new file in Powershell. Take note that the password for every generated account will be "Password1". Run the script and observe the accounts being created. Check the "_EMPLOYEES" folder in ADUC to see all of the created accounts.
</p>
<br />


<p>
Continued <a href="https://github.com/TrevorBrandtIT/dns-intuition">here.</a> Thank you for following along!
</p>
<br />
