<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Create our Resources( RG, VMs, Install WireShark)
- Observe ICMP Traffic
- Observe SSH Traffic
- Observe DHCP Traffic
- Observe DNS Traffic
- Observe RDP Traffic

<h2>Create our Resources</h2>

<p>
<img width="768" alt="image" src="https://github.com/PradoJuanPablo/azure-network-protocols/assets/160810181/84af229e-88d5-4538-8024-15218eb1a667">

<img width="769" alt="image" src="https://github.com/PradoJuanPablo/azure-network-protocols/assets/160810181/a440373b-6761-4917-882c-e51f23f53ed3">


</p>
<p>
To get started, we must first create our Resource Group(RG) and our Virtual Machines(VM). VM1 will run Windows 10. VM2 will run Ubuntu(Linux). However, both VMs will need to have the same:

- Resource Group (RG-Lab)
- Region: US East
- Vnet (VM1-vnet)

</p>
<br />

<p>
<img width="722" alt="image" src="https://github.com/PradoJuanPablo/azure-network-protocols/assets/160810181/655fa14a-3158-4aa9-a8c4-39a627e29759">

</p>
<p>
Next we need to install WireShark, to do that we log into our Windows machine (VM1). 

1. Type "Download WireShark" into browser
2. Select Windows x64 Installer
3. Open the file
4. Run everything by the default settings

Now we are ready to capture traffic!

</p>
<br />

<h2>Observe ICMP  Traffic</h2>

<p>
<img width="720" alt="image" src="https://github.com/PradoJuanPablo/azure-network-protocols/assets/160810181/1a728a55-8616-4797-a30c-4fa44965a7ff">

</p>
<p>
Lets observe ICMP traffic from VM1 to VM2. To do this we open up WireShark > filter to only see ICMP traffic > opem Powershell and type "ping *VM2's Private IP* -t"
Using "-t" we set a continous ping. Notice in WireShark we are getting requests and replys meaning a successful ping.

</p>
<br />

<p>
<img width="1241" alt="image" src="https://github.com/PradoJuanPablo/azure-network-protocols/assets/160810181/aadba1a6-028a-46f1-9eb6-1360089cf66f">

<img width="720" alt="image" src="https://github.com/PradoJuanPablo/azure-network-protocols/assets/160810181/c85e338a-c7b3-411d-a85f-8d60f17021f2">


</p>
<p>
Now we're going to set up a firewall rule for VM2 that's not going to allow ICMP traffic to pass thorugh. To do that we go back to the Azure Portal. 
  
1. Network Security Groups
2. Select VM2-nsg
3. Inbound Security Rules
4. Add, Create the rule
5. Save

Go back to WireShark and see how the requests stop going through and it shows "no response found!" Our firewall prevents any ICMP traffic from passing through.

To allow ICMP traffic to pass through again, head back to the DENY_ICMP_PING_FROM_ANYWHERE ruleand select allow. Which will allow ICMP requests to come in. 
<br />


<h2>Observe SSH Traffic</h2>

<p>
<img width="719" alt="image" src="https://github.com/PradoJuanPablo/azure-network-protocols/assets/160810181/8f5cdcc5-213b-44a6-b542-49643bd88d09">

</p>
<p>
Next we're going to SSh into VM2 and see the traffic passing in Wireshark. To do that we enter in "ssh labuser@10.0.0.5" which is the command used to ssh into our machine with our credentials.

We can see that whatever we enter in the terminal, the data can be seen on WireShark. 
</p>
<br />


<h2>Observe DHCP Traffic</h2>

<p>
<img width="720" alt="image" src="https://github.com/PradoJuanPablo/azure-network-protocols/assets/160810181/7ad1a03d-c7d4-4a06-9068-fe6f2450ba01">


</p>
<p>
Now we're going to inspect DHCP Traffic. DHCP is a protocol that automatically assigns IP addresses to devices on the network. 

On WireShark we see that a request made to the DHCP server (168.63.129.16) for a new IP address (ipconfig /renew). The DHCP server replies with a DHCP ACK packet and we got re-issued an IP address. 
</p>
<br />


<h2>Observe DNS Traffic</h2>

<p>
<img width="718" alt="image" src="https://github.com/PradoJuanPablo/azure-network-protocols/assets/160810181/f3e92223-b32e-4e02-9b20-9fa1fe86c2d3">

</p>
<p>
This time, lets inspect DNS traffic. On Powershell we're going to enter the command "nslookup www.google.com". The nslookup command, when used, asks the DNS server for information such as IP addresses and domain names on any given hostname. 
  
On WireShark we see the traffic looking for information about google and returning the information to us. 
</p>
<br />


<h2>Observe RDP  Traffic</h2>

<p>
<img width="719" alt="image" src="https://github.com/PradoJuanPablo/azure-network-protocols/assets/160810181/565cb0d4-2666-49fe-9d43-7f58c597c6d4">

</p>
<p>
Lastly, we're going to inspect RDP traffic. However, another way of filtering for RDP traffic is to type "tcp.port == 3389". This is because RDP uses TCP port 3389. 

Upon entering the filter, we see we're getting spammed with traffic, that is because we are currently in a live RDP session from our host computer to VM1. 

</p>
<br />

