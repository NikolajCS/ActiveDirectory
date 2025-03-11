# ActiveDirectory


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

<h2>Preparing Active Directory </h2>

- Step 1 Create a ressource group + a virtual network connect the ressource group to the Virtual Network
- Step 2 Create a Domain Controller VM > I will name it dc-1 > Put it in the same ressource group you just created, choose Windows server 2022
- Step 3 Make sure you choose the Virtual Network you just created for the Domain Controller VM
- Step 4 Create another VM, same region, same ressource group, same Virtual Network > I will name it client-1

<h2>Deployment and Configuration Steps</h2>

First we need to make the Domain Controller VM's private nic IP address to static > in porta.azure > Virtual Machines > click on the Virtual Machine > Network Settings > Click on Network Interface / IP Configuration > ipconfig1 > Set to Static

![image](https://github.com/user-attachments/assets/586804ee-c58d-4015-bc07-15251b1e88e6)

To test connectivity I will disable the firewall in the VM (This step is not necesarry) > Log in to the Domain Controller VM via Remote Desktop > windows + r >type "wf.msc" > enter > Click on windows firewall > Firewall State: off > Make sure you turn off the firewall state for all 3, Domain profile, Private profile and Public profile 

![image](https://github.com/user-attachments/assets/916998a6-b09d-403e-9185-f006d87db0a2)

Now we will set Client-1's dns settings to dc-1 (Domain Controller VM) private IP address > Get DC-1's private IP addresse > Go into Azure > Virtual Machines > Click on DC-1 > Cope pase the private IP address > Go into client-1 VM > Network Settings > Network Interface /IP Configuration > DNS Server > Custom > Paste DC-1's private IP adresse

![image](https://github.com/user-attachments/assets/a6623f4b-0fc7-4c6f-8964-0b804f678b32)

You may have to restart the VM > In Azure > Virtual Machines > Highlight client-1 > restart

We will now attempt to ping dc-1's private IP address from client-1 and make sure we can get a connection > get client-1's public IP address > log in to client-1 via Remote Desktop > get dc-1's private IP address from Azure > Open Powershell > ping "dc-1's IP address"

![image](https://github.com/user-attachments/assets/10aa6e9e-a688-4848-a211-e658a4c218c3)

We can also check that the DNS server matches dc-1's private IP address > Within Client-1 > Powershell > command "ipconfig /all" > Enter > The DNS server of Client-1 should be dc-1's private IP address

![image](https://github.com/user-attachments/assets/c438eb19-6298-4b24-8954-034f02c166d2)

<h2>Deployment and Configuration Steps</h2>

Log into dc-1 > Open Server Manager > Add roles and features > Press next until you get to "Server Roles" > Check "Active Directory Domain Services" > Next > Next > In "Confirmation" you can check "Restart the destination server automatically if required" > Install

![image](https://github.com/user-attachments/assets/ccc776ec-6ae4-488f-b139-7589a81a260e) 

We will now promote dc-1 to become an actual Domain Controller > Inside Server Manager on the top rightside click on notifications (Flag with a yellow triangle) > "Promote this server to a domain controller" 

![image](https://github.com/user-attachments/assets/2bd6d0a2-0193-4f32-abb3-be5c33f8d961)

Add a new forest > Name it "mydomain.com" > Next > Choose password > DNS Options > Uncheck "Create DNS delegation" > Press next until you get to "Prerequisites Check" > Install > You will be disconnected from the VM when the install is finished > Now that we have made the Virtual Machine into a Domain Controller you now have 2 options when you log into dc-1 > You can log in as "local user" the same way you did before. But if you want to login in as a domain user you need to type > mydomain.com\username or MYDOMAIN\username with the same password.

Creating a Domain Admin, Employees and Admins folder: Open the Windows serach bar and type "Active Directory Users and Computers"

![image](https://github.com/user-attachments/assets/58d4300a-eb96-4942-8e73-dc4df8cb99d1)

Right click on "mydomain.com" > New > Organizational Unit > Name it _EMPLOYEES > OK 

![image](https://github.com/user-attachments/assets/8341c632-508a-414c-9187-f6df313e2261)

Create another Organizational Unit > Name it _ADMINS > OK 

![image](https://github.com/user-attachments/assets/2d4a19ec-4b8a-4335-b1b1-1ff86c051cbc)

Create an Admin user > Right Click Admin folder > New > User > I will name my admin Jacob

![image](https://github.com/user-attachments/assets/0a61dce0-d0f1-4902-af18-76d589c1651a)

Making Jacob a Domain Admin > Admin Folder > Right click on Jacob (or whoever you want to make a Domain Admin) > Properties > Member of > Add > Type "domain admin" > Check Names > OK > Apply > OK > Now the chosen account is a Domain Admin

![image](https://github.com/user-attachments/assets/4572a56d-1cff-417d-bcf2-3c60cbe5659a)

Sign out of dc-1, we will now log into the Domain Controller as the new admin account we just created > So I will log in as: MYDOMAIN\jacob_admin if it is not letting you log in you can try mydomain.com@name_admin instead. If you want to check that you are using the right login you can check it inside Active Directory Users and Computers > Admin folder > right click the admin account > properties > Account > In here you can check the correct logon name

![image](https://github.com/user-attachments/assets/24f555b0-beff-4e82-a734-69f779b28504)

Log into dc-1 as the new admin. We will now make client-1 join the domain. Sign into client-1 with the original local admin account > right click the Windows icon > System > You should be in the "About" > Under "Related settings -> Rename this pc (Advanced) > Change > Under "Member of" change to Domain > Type mydomain.com > ok > You will need to use your domain admin account login to give client-1 permission to join the domain > You should then get a pop up message that says that you have joined the domain


![image](https://github.com/user-attachments/assets/9982e6f6-0b8e-4de6-b7cc-8a64ba7a9a35)

We will now verify that client-1 has joined the domain > Log into dc-1 > Open Active Directory Users and Computers > Expand mydomain.com > Click on Computer > In here you should see client-1

![image](https://github.com/user-attachments/assets/842f3682-cc81-43c2-b0f1-04232321e1d2)

We can make a Client folder and drag client-1 into it > right click mydomain.com > New > Organizational Unit > I will name it _CLIENTS > Go to Computers > Click on client-1 and drag it into the new folder "_CLIENTS" > You will get a pop up message, press yes.

![image](https://github.com/user-attachments/assets/d1e38753-6b42-4de4-9665-477b6a9f40be)


<h2>Creating Users with Powershell</h2>

We will now allow all domain users to log into client-1. First we need to log in to client-1 as the domain admin we created. I will log in asMYDOMAIN\jacob_admin > Right click Windows start > System > Remote Desktop or you can open windows search bar > Type settings > System > Remote Desktop > Select users that can remotely access this PC > Add > domain users > Check Names > OK 

![image](https://github.com/user-attachments/assets/09a6e3ec-b0b9-40eb-9932-ba368c0e37cd)

Now we will create 10000 users in Powershell > log into dc-1 as the domain admin > Open Powershell ISE as an Administrator > Click the link and copy the script, (this script creates 10000 user accounts) https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1 > Open a new script and paste the script we copied into it > Save the script on the Desktop > control + s or click the save icon on the top and save it to the desktop

![image](https://github.com/user-attachments/assets/3f349fad-469c-4d62-9b67-bec8f2bf8aba)

We can now start the script > In Powershell click the green triangle "run script" FYI you can stop the script at anytime inside Powershell if you do not care to have 10000 users> While the script is running we can pick a random user and log into client-1 > In dc-1 open Active Directory Users and Computers > Expand mydomain.com > _EMPLOYEES > Pick a random user, in my case I will pick the user "bec.bira". FYI the password of every user generated by the script is set to "Password1" 

![image](https://github.com/user-attachments/assets/55ed3b31-9d66-4edd-a725-071200683ea3)

You can sign out of client-1 > login my account will be MYDOMAIN\bec.bira > Password: Password1 > sign in > You should now be logged in as the User you chose 

![image](https://github.com/user-attachments/assets/8b675618-bd86-44b5-9c90-106901689bea)

<h2>Group Policy and Managing Accounts</h2>

First we will configure a failed password limit > Log in to dc-1 > windows key + r and type gpmc.msc > Expand "Forest: mydomain.com > Expand "Domains" > Expand mydomain.com > Highlight "Default Domain Policy and right click on it > edit 

  ![image](https://github.com/user-attachments/assets/0392e0a8-8e4b-4d22-8757-57e299491de6)

Expand "Computer Configuration" > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy > Account lockout threshhold > I will set mine to 5 attemps 

![image](https://github.com/user-attachments/assets/1eb6bda8-489f-419e-b7a6-e71b11348ee1)

It will take some time before the policy is automatically updated and propegated to all the computers. But we can also force an immediate update to a specific pc > Log in to client-1 as an admin > I will log in as MYDOMAIN\jacob_admin > Open cmd and type the commnad: gpupdate /force 

![image](https://github.com/user-attachments/assets/7b414396-97ba-4b36-b8ee-2f1a83f5cf8a)

I will choose a random user from the Domain and fail 5 logins to client-1, and then we will unlock the account > login to dc-1 and find the specific user that has been locked out under _EMPLOYEES > right click the user > Properties > Account > Check "Unlock account", hit apply and ok

![image](https://github.com/user-attachments/assets/690a7231-5d24-4054-a843-d620e784e656)

Resetting passwords > Find the individual user > right click > reset password > OK: You can also disable and enable accounts the same way > Right click user > "Disable", "Enable" 

![image](https://github.com/user-attachments/assets/54c1dd4c-9ca2-4a8d-8acd-9dd9ca1a694a)

Observing Logs: Log into client-1 > Open windows search bar and type "Event Viewer", (if you are not logged in as an administrator you will need to run the program as an administrator to get full access > Expand "Windows Logs > Securty > In here you can see all failed/successfull logon attemps and what user failed the logon. So in my case you can see "Audit failure" which means a failed login and you can see the user baq.porin failed the logon. 

![image](https://github.com/user-attachments/assets/c480fea8-8828-4b69-9497-47fd60ee2f1c)






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

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
