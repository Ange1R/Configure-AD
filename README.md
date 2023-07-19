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

- Setup Resources in Azure
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create an Admin and Normal User Account in AD
- Join Client-1 to your domain (mydomain.com)
- Setup Remote Desktop for non-administrative users on Client-1
- Create a bunch of additional users and attempt to log into client-1 with one of the users


<h2>Deployment and Configuration Steps</h2>

Step 1: Create two virtual machines in Azure
<p>
The first VM will be named DC-1 (Domain Controller) and use Windows Server 2022 as its image
<img Screenshot 2023-07-19 at 12 47 39 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/bc9ed8a2-c701-4330-9196-feb72281d03c">
</p>
The second VM will be named Client-1 and use Windows 10 as its image (ensure that both VMs are located in the same resource group)
<p>
<img Screenshot 2023-07-19 at 12 57 19 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/cd53cd2b-b40a-4bf8-aea2-2667a36b4b93">
</p>
<br />

Step 2: Navigate back to DC-1 VM to change its network interface to static - On the left side menu click "Networking" - Find Network Interface and click it - On the left side menu choose IP configurations - Scroll down to choose the Ipconfig - Switch allocation from Dynamic to Static
<p>
<img Screenshot 2023-07-19 at 1 07 47 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/0e233300-03c7-48f5-b675-627890f861dd">
</p>
<img Screenshot 2023-07-19 at 1 08 39 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/59822e0d-c5bb-4247-bd41-bd3b3e00cad7">
<p>
<img Screenshot 2023-07-19 at 1 08 55 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/b78e12a1-8bad-4f3e-a124-34018176d003">
</p>
<img Screenshot 2023-07-19 at 1 09 13 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/449b8392-33e0-47d3-b45d-4ae1024bc446">
<p>
<img Screenshot 2023-07-19 at 1 09 26 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/d7bb7769-f65e-4b5b-a231-1f76c6a43deb">
</p>
<br />

Step 3: Ensure Conectivity Between the Client and Domain Controller
<p>
Login to Client-1's VM via remote desktop and ping DC-1's Private IP address with the command "ping -t" using command prompt
</p>
<img Screenshot 2023-07-19 at 1 26 30 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/f00f44e1-4ee6-480d-b1bd-d18638a64352">
<p>
You'll notice that the request will time out because Windows firewall on DC-1's VM is blocking ICMP traffic
<img Screenshot 2023-07-19 at 1 30 24 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/e7029ac1-ea41-404a-bcf9-623b8fd3fed0">
</p>
Minimze Client-1's VM and open DC-1's VM using its public IP address and search wf.msc to open Windows firewall
<p>
<img Screenshot 2023-07-19 at 1 39 43 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/2a6421a9-a153-4f1e-980b-4f63ea02788c">
</p>
Choose "Inbound Rules" located on the left side menu - Sort by protocol - Locate and enable the Core Networking Diagnostics using ICMPv4 
<p>
<img Screenshot 2023-07-19 at 1 44 47 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/90ea5170-32cb-4beb-bed1-dc8d293eb3a4">
</p>
<img Screenshot 2023-07-19 at 1 45 17 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/89f99487-1d01-4dcf-8df2-ceb5d7ac0e73">
<p>
<img Screenshot 2023-07-19 at 1 45 54 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/7826314a-b693-46e3-a931-0112f77bbd38">
</p>
Head back over to Client-1's VM and you should notice that the ping should be working now
<p>
<img Screenshot 2023-07-19 at 1 50 44 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/c77b17c8-1860-4ca2-a0c1-18fe24c626b8">
</p>
<br />

Step 4: Install Active Directory on DC-1's VM using Server Manager
<p>
<img Screenshot 2023-07-19 at 1 54 56 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/ad921df9-45c3-4970-b08e-1c02c5d80a0d">
</p>
Select Add Roles and Features
<p>
<img Screenshot 2023-07-19 at 2 05 54 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/364f686c-abb3-4f7f-b187-8b6b7ae42dd8">
</p>
Click next until you reach Server Roles and check the box for "Active Directory Domain Services" then continue clicking next and install 
<p>
<img Screenshot 2023-07-19 at 2 07 53 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/05e200c1-b3fe-4882-96fc-a011f1e433db">
</p>
Navigate to the caution sign once finished installing and select "Promote this server to a domain controller"
<p>
<img Screenshot 2023-07-19 at 2 09 31 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/ffb7557e-9b96-444c-b407-c22691b1cd0b">
</p>
In Deployment Configuration add a new forest and name the domain - Create password - Click next and install when prompted (This process will automatically restart the VM and will make you resign via remote desktop)
<p>
<img Screenshot 2023-07-19 at 2 13 12 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/e7139a1c-d3ed-4817-a49a-1158b96d2d09">
</p>
<img Screenshot 2023-07-19 at 2 14 45 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/031cc491-926d-41cb-bfa4-9fa777596da2">
<p>
<img Screenshot 2023-07-19 at 2 17 58 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/ffc22149-afb7-4be0-a3e7-b052663e4344">
</p>
<br />

Step 5: Open AD Users and Computers to add Organizational Units and Users
<p>
<img Screenshot 2023-07-19 at 2 29 59 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/34116484-b25d-435f-868b-80caadc8fa86">
</p>
<img Screenshot 2023-07-19 at 2 31 59 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/24fecacb-cb9e-488a-8096-72aa24287182">
<p>
Locate and select the domain you created on the left side menu - Add two organizational units (_EMPLOYEES & _ADMINS)
</p>
<img Screenshot 2023-07-19 at 2 34 32 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/d943a9b3-e89a-4e8e-9c71-7155edc1a0e3">
<p>
<img Screenshot 2023-07-19 at 2 36 42 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/4a923d2a-fe90-4f24-a98b-bb72293f60e6">
</p>
Add an Admin to the organizational unit you just created
<p>
<img Screenshot 2023-07-19 at 2 42 07 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/33a33c55-ceed-4240-8ec9-7474bd511200">
</p>
<img Screenshot 2023-07-19 at 2 44 02 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/4e62ecc9-1a64-4a42-ab8a-8834b342209f">
<p>
<img Screenshot 2023-07-19 at 2 44 42 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/a8852e3e-476b-4a17-964d-6d7de3defd97">
</p>
Assign user as an admin - right click on user and select properties - Member of tab - Add - Enter Domain Admins into the space provided - Check names - OK - Apply - OK
<p>
<img Screenshot 2023-07-19 at 3 06 07 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/6a5fdfb0-a8cf-4e85-bd36-7a3b1c1afb9d">
</p>
<img Screenshot 2023-07-19 at 3 08 11 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/23e5c21e-4d4d-43a6-b2e3-fafc30d395db">
<p>
<img Screenshot 2023-07-19 at 3 10 51 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/9d1e8350-3dd7-4cb4-bd07-f613c7c4abaa)">
</p>
<br />

Step 6: Log out of DC-1 and log back in as the Admin that was created using (mydomain.com/jane_admin)| You can verify you're on the correct user or host by opening the command prompt and typing "whoami" or "hostname"
<p>
<img Screenshot 2023-07-19 at 3 20 49 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/4f083968-4610-4d4c-ab08-fa86a920dfdb">
</p>
<br />

Step 7: Join Client-1 to the domain
<p>
Retrieve DC-1's private IP adress in Azure
</p>
<img Screenshot 2023-07-19 at 3 34 46 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/8fb7b13e-9fe6-417f-9eac-17fb08e86870">
<p>
Head over to Client-1 in Azure - Networking - Network Interface - DNS Servers - Custom - Type in the private IP address you retrieved from DC-1 - Save
</p>
<img Screenshot 2023-07-19 at 3 40 55 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/7f93a3b5-0c76-4fdb-ba14-e630aea560ff">
<p>
<img Screenshot 2023-07-19 at 3 42 43 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/700c4482-e621-4613-b88f-de0b324ef4a6">
</p>
Once saved, go back to Client-1 in Azure and click "Restart" to restart Client-1 and log back into Client-1's VM with the original admin credentials (Labuser)
<p>
<img Screenshot 2023-07-19 at 3 50 11 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/b0e5bc6d-37c1-409e-ac6f-1d093870368d">
</p>
Open systems settings - Locate "Rename this PC (advanced) on the right side menu - click the change button - select Domain - Type mydomain.com - OK
<p>
<img Screenshot 2023-07-19 at 4 01 49 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/49dddb01-bc0d-43b3-be11-e44af01bfc78">
</p>
Enter in the credentials of mydomain.com\Jane_admin and the VM will restart (Resign in via remote desktop with the mydomain.com\jane_admin credentials)
<p>
<img Screenshot 2023-07-19 at 4 06 55 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/627aef20-700b-4109-a14c-21d991f304e0">
</p>
<br />

Step 8: Setup Remote Desktop for non-administrative users on Client-1
<p>
In Client-1's VM open system settings - Remote Desktop - Select Users that can remotely access this PC - Add - Type Domain Users - Check Names - OK - OK
</p>
<img Screenshot 2023-07-19 at 4 15 42 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/0f72694a-4290-4cd3-a344-355f2125a26c">
<p>
<img Screenshot 2023-07-19 at 4 22 57 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/783c8f51-139d-48ef-a27d-f80ff27ad83b">
</p>
<br />

Step 9: Create additional users and attempt to log into Client-1 with one of the created users
<p>
Head over to DC-1's VM with the jane_admin credentials and run PowerShell_ise as an adminstrator 
</p>
<img Screenshot 2023-07-19 at 4 33 11 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/ace55520-8f85-4200-a0ed-01d706527a41">
<p>
Using your host machine, copy the script located <a href="https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1">Here </a> - Head back to DC-1 - Select new file in the top left corner of Powershell ISE - Paste the script - Click the green triangle to run the script which creates the users
</p>
<img Screenshot 2023-07-19 at 4 41 05 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/7df3d02c-cf5f-4417-a056-d28e6992d52f">
<p>
<img Screenshot 2023-07-19 at 4 43 51 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/3d83efcc-ecc7-46c0-ba50-5221caaaf904">
</p>
Reopen AD Users and Compters - Navigate to the _EMPLOYEES organizational unit - Click any random User - Copy their display name
<p>
<img Screenshot 2023-07-19 at 4 49 40 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/d658debc-f787-40e7-924f-740cba9cc49b">
</p>
Head over to Client-1's VM - Logout - Reconnect to Client-1 with the credentials of the user that was just created (mydomain.com\jek.xit | PW: Password1)  
<p>
<img Screenshot 2023-07-19 at 4 54 58 PM" src="https://github.com/areyes302/Configure-AD/assets/139584521/b76d9d96-5877-47c4-ac7f-9e912be4ba22">
</p>
<br />

CONGRATULATIONS🎉, you have accurately implemented Azure's Active Directory 
