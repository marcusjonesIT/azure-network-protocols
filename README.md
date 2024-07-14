<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we're going to create 2 VMs in Azure and observe various network protocols within Wireshark as well as experiment with Network Security Groups. <br />




<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (ICMP, SSH, DHCP, DNS, RDP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used</h2>

- Windows 10 (22H2)
- Ubuntu Server 20.04

<h2>Part 1 (Create Our Resources in Azure)</h2>

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/f37aa6a0-6c59-4b0e-a184-ee156f9ba5a6)
<p>
Select the Subscription you would like to use (Azure subscription 1), Name the Resource Group (ex: RG-LAB01) and choose the region you would like the resources to be created (ex: East US) then select Review & Create  
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/7b3ba4ef-031a-4dd0-bc0d-66f3d0a30a90)
<p>
We will be creating 2 Virtual Machines, so we'll create the Windows VM first. Select the Resource Group we just created (RG-LAB01) then name the VM and just to keep it simple we can name it (VM1). Select the same region that we created the Resource Group in which was (East US) and for the Image we will select (Windows 10 Pro, version 22H2 - x64 Gen2) since we're creating a Windows machine. 
</p>
<br />


![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/0cfd94d2-1296-4929-bdd6-17f3305902c7)
<p>
Next we will select the size for the VM which essentially determines the performance of the VM you're creating. Since we are creating 2 VMs you want to choose at least 2 vcpus and at least have 16 GiB of memory (Standard_DC2s_v3 - 2 vcpus, 16 GiB memory ($140.16/month). Choose a Username and Password (Username: labuser). (NOTE: Take note or remember what you choose because you will need it later on in the tutorial.) Check the Licensing box and click Next until you get to the Networking Settings. 
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/b3a6a629-f7a6-4883-9408-f4b04ed00814)
<p>
This is where the Virtual Network and Subnet will be created automatically. The VM will have 2 IP Addresses, your Public IP Address and the Subnet (10.0.0.4/24) which is the Private IP Address. Click Review + Create. 
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/1862e1a7-3ab0-4ea4-8c68-c6835e53ee21)
<p>
This isn't necessarily a step but here you can see the Resources such as the VM, NIC, Network Security Groups and Public IP Addresse are being created within the Resource Group we created.
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/98fc685d-987e-439a-bafa-07f157f8993d)
<p>
We'll use the same steps above to create our second VM except this machine will be running Ubuntu. So for Demo purposes, we will name the machine (VM2). Select the same region as our Resource Group and other VM (East US) and for the Image we will select (Ubuntu Server 20.04 LTS - x64 Gen2)
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/6abbad33-882f-40ee-8e5b-8b0b1900b686)
<p>
By default SSH public key will be selected for the Authentication but we're going to select Password so we can create a username & password to login to the VM later. So for the username we will use (labuser) and set the password to whatever you like just keep note of whatever you do create. Select Next until you get to the Network Settings.
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/f7b19b05-23d6-49a3-9a8c-23cb41b8c71e)
<p>
The Virtual Network should automatically select the network created for VM1, if not change it to (VM1-vnet). Select Review & create.    
</p>

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/6478b69f-8cb6-4736-9034-66654ddc1ff7)
<p>
Now that we have both virtual machines up and running we're going to connect to our Windows machine(VM1) using the Remote Desktop Connection App. Click the start menu and type remote and the app should pop up. Copy and paste the Public IP Address (20.115.54.153) the click connect. 
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/0da63d9e-024b-4b93-8531-d160af04d830)
<p>
Select "More choices" then enter the username & password was created earlier and connect to the desktop. There will be a popup after you enter the credentials just select yes to continue connecting to the VM. 
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/32594248-94ed-4fac-b76b-8a020be59f43)
<p>
The privacy settings don't really matter in the lab so you can deselect the settings and click Accept. 
</p>
<br /> 

<h2>Part 2 (Network Observations) Starting with ICMP Traffic</h2>
We are going do download and install Wireshark to observe network traffic between the VMs. "Wireshark is a free and open-source packet analyzer. It is used for network troubleshooting, analysis, software and communications protocol development, and education."

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/12c58a59-3ca0-4df0-acdd-de50bb64eb65)
<p>
Go to www.google.com and type "Download Wireshark".  Select the first link and then select (Windows x64 Installer) under Stable Release. Download the file and click Next all the way through until the program download and install then click Finish. 
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/c6af16ab-ace9-4b00-ac6e-7896e546aa51)
![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/d090d5c8-902e-4e4c-8dc2-6900c4400c44)
<p>
Open Wireshark, select Ethernet and then click the blue icon that looks like a fin to start capturing packets to observe live Network Activity on the VM.  
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/8d198d91-9555-436a-ab87-b06891fd6b81)
<p>
Now we're going to filter for ICMP traffic only and this is where the other VM we created will come into use. ICMP is used the protocol ping uses, ping is used to test connectivity to differents hosts on the internet or network. So we're going to be checking traffic between both VMs we created. 
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/9595cfb7-e45b-4725-97ee-3327c025a9a3)
<p>
We are going to retrieve the Private IP Address of our Ubuntu VM which is 10.0.0.5 and use that to ping it from our Windows VM using CMD or Powershell whichever you prefer and watch the traffic within wireshark. So I'll be using Powershell for this example.    
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/72898807-bd17-44d9-98b6-bd49828a5cd7)
![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/e2e15b23-50b5-4a2a-9f54-609a93013cc4)
<p>
Now we will ping VM 2 using the Private IP 10.0.0.5 and observe the traffic between the two VMs. We should see 4 packets sent and received. You can see where both VMs are requesting and replying across the Network. To take it a step further you can ping any URL for example www.disney.com and observe the traffic.   
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/bdbf876b-9750-458c-b135-e7ecc59c9ae6)
<p>
We're going to initiate a perpetual/non-stop ping from our Windows 10 VM to the Ubuntu VM using the following command (ping 10.0.0.5 -t). Then while it is pinging we're going to change the Firewall on VM2 to block incoming (inbound) ICMP traffic because ping uses the ICMP protocol and then once we do that we should stop receiving echo replies from VM2. 
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/ae4d5b1d-6a9c-4421-bb85-21a1bb7f8e63)
![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/947c79dd-c86f-4292-9123-ea118670578b)
<p>
Open the Network Security Group settings for VM2 and select Inbound Security Rules, Click add and we're going to create the new rule to block incoming ICMP traffic. Then we're going to use the settings in the picture above for the rule. Change the Protocol from "Any" to "ICMPv4", change action to "Deny" and then for Priority we're going to change it to 200 to allow this rule to be evaluated before any other rule so it can block the ICMP traffic. 
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/016f448f-44d8-4431-bd3e-3c4c3e2721b9)
<p>
Navigate back to the Windows VM and you can now see that all inbound ICMP traffic is being blocked after applying the rule that we created. 
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/a6255567-1480-4bb9-a109-df3764578b2f)
<p>
Now we will go back to the NSG settings for VM 2 and allow inbound ICMP traffic and you will be able to see the pinging activity as shown above in the picture. To interupt the non-stop ping you can hit ctrl + c and the ping activity will stop. 
</p>
<br />

<h2>Observe SSH Traffic</h2>

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/e2ac14eb-cffb-401a-9d42-c29974bbe0fe)
<p>
Navigate back to Wireshark and filter for SSH traffic only.   
</p>
<br />

![image](https://github.com/marcusjonesIT/azure-network-protocols/assets/174873189/7f899cef-a69a-4588-94f3-095c220b006a)
<p>
From the Windows VM we're going to "SSH into" the Ubuntu VM via it's Private IP Address using (ssh labuser@10.0.0.5) as shown above.  
</p>
<br />

![image](https://github.com/user-attachments/assets/7a4b1034-3050-4a86-a938-4a979074837a)
<p>
Immediately when we try to initiate the SSH connection you will observe some SSH traffic since we are filtering for SSH in Wireshark.
</p>
<br />

![image](https://github.com/user-attachments/assets/37eaead4-370a-43dc-8ff5-6000eb2a21b2)
<p>
It will ask you if you are sure you want to connecting, type yes and press enter. When entering the password it's not going to actually show the password on the screen so just know when you're typing the password it is going into the terminal. Note: If you think you messed up just hit enter once and fail it then enter the password again or just hit backspace a bunch of times.
</p>
<br />

![image](https://github.com/user-attachments/assets/62dfad61-302f-41e0-a999-0e2b63b6e9ee)
<p>
Now that we're logged in you can type commands (id, uname -a, pwd, etc.) in the Linux SSH connection and observe SSH traffic spam in Wireshark. When you are done just type exit and hit enter to close the connection. 
</p>
<br />

![image](https://github.com/user-attachments/assets/1426026e-a507-4d57-8573-8b9613072f40)
![image](https://github.com/user-attachments/assets/a329ab42-6b3a-4bd1-8f48-fd73678f5ba7)

<p>
Next we're going to observe DNS traffic so type "DNS" into the filter the press enter. We will use the "nslookup" command which will pretty much ask the DNS server what the IP Address is for any given Host name. For example (nslookup www.google.com or nslookup www.espn.com) this essentially asks our DNS server what the IP Addresses for Google and ESPN. Note: Instead of typing DNS in the filter you can also use (UDP.port==53) to observe the DNS traffic because DNS uses Port 53.
</p>
<br />




















































