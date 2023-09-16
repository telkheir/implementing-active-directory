<p align = "center">
<img src = "https://github.com/telkheir/implementing-active-directory/assets/145223639/a1661576-4749-4f20-b3be-2fa3ca3312b4">
</p>

<h1> Setting up Active Directory in the Cloud </h1>

This tutorial will walk through the implementation of Active Directory using Azure virtual machines.


<h2> Environments and Technologies Used </h2>
	<ul>
	  <li>Microsoft Azure</li>
	  <li>Virtual Machines</li>
	  <li>Active Directory Domain Services</li>
	  <li>Powershell</li>
	</ul>

<h2> Operating Systems Used </h2>
	<ul><li>Windows 10</li>
	    <li>Windows Server 2022</li>
	</ul>


<h2> Navigation </h2>
	<ol>
	    <li><a href = "#step_1">Set up virtual machines</a></li>
	    <li><a href = "#step_2">Install Active Directory Domain Services on the domain controller (DC-1)</a></li>
	    <li><a href = "#step_3">Set up an admin account</a></li>
	    <li><a href = "#step_4">Join a device to the domain</a></li>
	    <li><a href = "#step_5">Allow all domain users access to Client-1</a></li>
	</ol>


<h2> Installation Steps </h2>
	<ol>
	  <li><h3 id = "step_1">Set up virtual machines</h3>
		We need two machines to begin, a windows server acting as the domain controller (named "DC-1" in the example) and one running Windows 10 (named "Client-1"). Make sure they are set up in the same region and virtual network.
		<br><br>
		<img width="764" alt="dc1-setup" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/9c38ada9-260f-45e0-baec-a0e15fe96c46">
		<img width="764" alt="client1-setup1" src="https://github.com/banksii/implementing-active-directory/assets/120074266/3573c28c-ebcb-4740-8e09-008b2f208621">
		<img width="581" alt="client-1-on-dc-network" src="https://github.com/banksii/implementing-active-directory/assets/120074266/a52e548f-f735-4ab0-b362-052ec672c6cf">
		<blockquote>
			Client-1 needs to be on the same network as the domain controller.
		</blockquote>
		Assign the domain controller (DC-1) a static IP address. To do this, go to the networking tab of the DC-1 controller and navigate to the network interface link highlighted below. From there, click on IP configurations under Settings in the left menu and set the private IP address of 'ipconfig1' to static.
		<br>
		<br>
		<img width="946" alt="dc-1-static-ip-" src="https://github.com/banksii/implementing-active-directory/assets/120074266/ef22fc0a-2d20-4c24-a328-5a9514233e19">
 		<img width="947" alt="dc-1-checking-priv-ip" src="https://github.com/banksii/implementing-active-directory/assets/120074266/a9f05dbb-2f2d-4e99-b1cd-8a77b676cb65">
		<img width="948" alt="dc-1-setting-priv-ip-to-static" src="https://github.com/banksii/implementing-active-directory/assets/120074266/3f7143a6-9f72-427a-95e4-5e53984b5ef5">
  		<br><br>
		To ensure connectivity between DC-1 and Client-1, I used Remote Desktop to access Client-1 and ping DC-1's IP address. The ping failed because DC-1 has a firewall up. To allow for ICMP traffic, I used Remote Desktop to access DC-1 and enable echo requests in Windows Defender Firewall with Advanced Security, after which I tried to ping DC-1 from Client-1 to confirm that there were no further issues.
		<br><br>
		<img width="915" alt="dc-1-enable-echo" src="https://github.com/banksii/implementing-active-directory/assets/120074266/fc3f8fd6-a06e-41ef-8c60-721555483232">
	  </li>
	  <li><h3 id = "step_2">Install Active Directory Domain Services on the domain controller (DC-1)</h3>
		  Navigate to "Add Roles and Features" in the server manager under "Manage"(top right). Continue through the wizard until you reach the Server Roles section and select "Active Directory Domain Services". Click Next and complete the installation.
		  <br><br>
		  <img width="960" alt="dc-1-install-active-directory" src="https://github.com/banksii/implementing-active-directory/assets/120074266/5e96f3f2-e5e8-42e8-a422-0f8e6ecf7760">
		  <br><br>
		  Back in the server manager, click on the flag icon with the caution sign. From the drop-down menu, click the link to promote the server to a domain controller.
		  <br><br>
		  <img width="915" alt="dc-1-promote-to-domain-controller" src="https://github.com/banksii/implementing-active-directory/assets/120074266/21d8bc5e-57eb-469c-a9ad-3638503e137e">
		  <br><br>
		  In the configuration wizard, select the option to add a new forest and give the domain a name. I chose tekdomain.com in this example. 
		  <br><br>
		  <img width="917" alt="dc-1-naming-domain" src="https://github.com/banksii/implementing-active-directory/assets/120074266/71691bca-acba-47b3-93b3-c0f20356a062">
    		  <br><br>
		  The next section should prompt the user to set up a password. Continue with the wizard and complete the installation. The device may restart.
	  </li>
   	  <li><h3 id = "step_3">Setting up an Admin account</h3>
      		  Active directory can be accessed through the server manager from the Tools drop-down menu.
	  	  <br><br>
      		  <img width="917" alt="accessing-active-directory" src="https://github.com/banksii/implementing-active-directory/assets/120074266/72e745cb-c50c-45b4-9f0c-14d575714f17">
	  	  <br><br>
		  Next, right click on the domain name, go to New, and select Organizational Unit. In this example, I created two organizational units called "Admins" and "Employees".
		  <br><br>
      		  <img width="566" alt="active-directory-new-org" src="https://github.com/banksii/implementing-active-directory/assets/120074266/cfdff660-90e7-425c-984f-48ebde94c3be">
	  	  <br><br>
		  Right click on the new Admins folder, go to new, and select user. You will be prompted to create a username and password.
		  <br><br>
    		  <img width="567" alt="image" src="https://github.com/banksii/implementing-active-directory/assets/120074266/6a4625a7-527b-4dc9-82eb-6f2307c9ca0f">
		  <br><br>
    		  This created a new user but they are not an admin yet. To assign them, go to that user's properties and navigate to the "Member of" tab. From there, click Add and type 'domain' into the box and click Check Names beside it. Click the domain option. This process is shown in the video below. You can now log out of the domain controller and log back in as an admin.
		  <br><br>
		  
https://github.com/banksii/implementing-active-directory/assets/120074266/18b3107b-ad14-42f7-b254-64bea69f0876
</li>
	<li><h3 id = "step_4">Joining a device to the domain</h3>
 		In this step, we're going to join the Client-1 virtual machine created in <a href = "#step_1">step 1</a> to the domain. To do this, Client-1 needs to be connected to the domain server with the static IP assigned to DC-1 in <a href = "#step_1">step 1</a>. Back in Azure, navigate to the Networking tab of Client-1. Click the network interface link and go to the DNS servers tab. From there, select the custom option and input the private IP address of DC-1 and save. The Client device will need to be restarted.
   		<br><br>
		<img width="678" alt="client-joining" src="https://github.com/banksii/implementing-active-directory/assets/120074266/d1748b65-2ed5-4559-9519-2f865b547ff0">
		<br><br>
  		Back on the Client-1 device, go to system settings and follow the steps demonstrated in the video below to assign the Client to the domain created earlier.
    		<br><br>
		
https://github.com/banksii/implementing-active-directory/assets/120074266/680a47dd-640a-4092-9d78-f314cb350220
		
Client-1 is now a member of the domain and you should be able to log in to the client's computer with an admin's credentials.
	</li>
 	<li><h3 id = "#tep_5">Allow all domain users access to Client-1</h3>
	</li>
</ol>
