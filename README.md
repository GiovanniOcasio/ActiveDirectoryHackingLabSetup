# ActiveDirectoryHackingLabSetup
Setup a Active Directory lab to prepare for the OSCP

This is an instructional on how to set up an Active Directory lab. In my case I am using it for my preparation for the Offensive Security Certified Professional exam, but it can be used for any purpose.

Install VMware Workstation 16 Pro from: https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html
Install a Windows Server ISO and Windows Client ISO (I used Windows Server 2019 and Windows 10 Pro): 
Windows 2019 Server: https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019
Windows 10 Pro: https://www.microsoft.com/en-us/software-download/windows10

If you are interested in product keys you can use Google to find valid keys.

Once VMware Workstation 16 is installed and the Windows ISOs are downloaded we can begin.

Open VMware Workstation
In VMware Workstation select New Virtual Machine 
Click Next
Select "I will install the operating system later."
Under Guest Operating System select Microsoft Windows and under Version select Windows Server (Version) and Windows (Version) for the client
Give the Virtual Machine a name and click Next
Set the disk size (I used the Maximum Disk Size)
Select "Store virtual disk as a single file"
Select Customize Hardware...
Set Memory to 4GB or 4096 MB
Set Processor to 4 (Server)/ 2 (Client)
New CD/DVD (SATA) Select "Use ISO image file" and select the Windows Server ISO and Cl 
Select Finish

Repeat for Client with the following changes:
Set Processor to 4 (Server)/ 2 (Client)
New CD/DVD (SATA) Select "Use ISO image file" and select the Windows 10 ISO

Click through the prompts 

Select Custom Install and select the only disk and Next

Assign the password for the server and begin the install 
For the client, after the install select assign to a Domain and create a username and password.

Installation is complete.

**Active Directory Setup On Server**

Power on the Windows Server
Login

1. Use 'sconfig' to:
    - Change the hostname
    - Change the IP address to static
    - Change the DNS server to the Server's IP address
2. Run Powershell:
    - Run 'Install-WindowsFeature AD-Domain-Services -IncludeManagementTools'
    - Run 'import-Module ADDSDeployment'
    - Run 'Install-ADDSForest'
    - Provide Domain Name
    - Set a Safe Mode Password
    - Press Y to restart after installation

3. Change the DNS setting on the Server with sconfig to match the IP address

**Connect Windows 10 to the Domain**

Next we'll open up the Windows 10 VM
First we'll change the DNS Settings to match the server

Open Powershell as Admin:
1. Run 'Get-NetIPAddress'
2. Note InterfaceIndex under IP Address for Ethernet0
3. Check current DNS Settings with 'Get-DnsClientServerAddress'
4. Set new DNS Server IP with 'Set-DnsClientServerAddress -InterfaceIndex (Step 2 Value) -ServerAddresses [Server IP Address]'
5. Confirm change with 'Get-DnsClientServerAddress'

In the 'Type here to search' type 'Access Work or School'
Click Connect
Click 'Join this device to a local Active Directory domain'
Type the Domain name and click Next
Create credentials for account
Establish the Account as Administrator







