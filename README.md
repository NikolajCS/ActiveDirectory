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

<h2>Pre Configuration Steps</h2>

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
