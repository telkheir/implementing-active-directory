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
		We need two virtual machines to begin, a windows server acting as the domain controller (named "DC-1" in the example) and one running Windows 10 (named "Client-1"). Make sure they are set up in the same resource group, region, and virtual network. I allotted both virtual machines at least 2 VCPUs and 8 GB memory.
		<br><br>
		<img width="764" alt="dc1-setup" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/9c38ada9-260f-45e0-baec-a0e15fe96c46">
		<img width="767" alt="client1-setup" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/70a32856-90d7-4999-a305-0c7a7c73b7be">
		<img width="581" alt="client-1-on-dc-network" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/98965631-bd10-4e85-85b1-2fb474f582f6">
		<blockquote>
			Client-1 needs to be on the same network as the domain controller.
		</blockquote>
		Assign the domain controller (DC-1) a static IP address. To do this, go to the networking tab of the DC-1 controller and navigate to the network interface link highlighted below. From there, click on IP configurations under Settings in the left menu and set the private IP address of 'ipconfig1' to static.
		<br>
		<br>
		<img width="960" alt="dc-1-static-ip" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/7f69e80e-650a-4b98-afbf-4e3d7cfba5a7">
 		<img width="947" alt="dc-1-checking-priv-ip" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/a1b12589-2bb0-4ac2-a447-837b22871159">
   		<img width="948" alt="dc-1-setting-priv-ip-to-static" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/4fee490a-1ae5-4559-b3c0-50f52a7b1532">
  		<br><br>
		To ensure connectivity between DC-1 and Client-1, I used Remote Desktop to access Client-1 and ping DC-1's IP address. The ping failed because DC-1 has a firewall up. To allow for ICMP traffic, I used Remote Desktop to access DC-1 and enable echo requests in Windows Defender Firewall with Advanced Security, after which I tried to ping DC-1 from Client-1 to confirm that there were no further issues.
		<br><br>
		<img width="915" alt="dc-1-enable-echo" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/21e90164-9fae-41d0-9f64-a1aeac24e272">
	  </li>
	  <li><h3 id = "step_2">Install Active Directory Domain Services on the domain controller (DC-1)</h3>
		  Navigate to "Add Roles and Features" in the server manager under "Manage"(top right). Continue through the wizard until you reach the Server Roles section and select "Active Directory Domain Services". Click Next and complete the installation. All the other sections in the wizard can be left on default.
		  <br><br>
		  <img width="960" alt="dc-1-install-active-directory" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/475aaa2e-f908-4f34-8a83-f780a6470f7c">
		  <br><br>
		  Back in the server manager, click on the flag icon with the caution sign. From the drop-down menu, click the link to promote the server to a domain controller.
		  <br><br>
		  <img width="915" alt="dc-1-promote-to-domain-controller" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/339e170c-14b9-4b5c-b69d-bdcb7a35b6e5">
		  <br><br>
		  In the configuration wizard, select the option to add a new forest and give the domain a name. I chose tekdomain.com in this example. 
		  <br><br>
		  <img width="917" alt="dc-1-naming-domain" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/907e1c24-ec67-4c2a-92c5-dfe611acd357">
    		  <br><br>
		  The next section should prompt the user to set up a password. Continue with the wizard and complete the installation. The virtual machine may need to restart to apply all the changes.
	  </li>
   	  <li><h3 id = "step_3">Setting up an Admin account</h3>
      		  Active Directory Users and Computers can be accessed through the server manager from the Tools drop-down menu.
	  	  <br><br>
      		  <img width="917" alt="accessing-active-directory" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/2a78692a-eb79-4471-a6e2-a4415a27f579">
	  	  <br><br>
		  Next, right click on the domain name, go to New, and select Organizational Unit. In this example, I created two organizational units called "Admins" and "Employees".
		  <br><br>
      		  <img width="566" alt="active-directory-new-org" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/7b892fc6-6b9c-4688-9dbb-b10477434288">
	  	  <br><br>
		  Right click on the new Admins folder, go to new, and select user. You will be prompted to create a username and password.
		  <br><br>
    		  <img width="567" alt="active-directory-new-user" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/e96d3649-e65f-40cb-941e-a5b5b05f3c92">
		  <br><br>
    		  This created a new user but they are not an admin yet. To assign them, go to that user's properties and navigate to the "Member of" tab. From there, click Add and type 'domain' into the box and click Check Names beside it. Click the domain option. This process is shown in the video below. You can now log out of the domain controller and log back in as an admin.
		  <br><br>
		  
https://github.com/telkheir/implementing-active-directory/assets/145223639/66984d36-98e6-4914-a7f3-9d15542128e0
</li>
	<li><h3 id = "step_4">Joining a device to the domain</h3>
 		In this step, we're going to join the Client-1 virtual machine created in <a href = "#step_1">step 1</a> to the domain. To do this, Client-1 needs to be connected to the domain server with the static IP assigned to DC-1 in <a href = "#step_1">step 1</a>. Back in Azure, navigate to the Networking tab of Client-1. Click the network interface link and go to the DNS servers tab. From there, select the custom option and input the private IP address of DC-1 and save. The Client device will need to be restarted.
   		<br><br>
		<img width="678" alt="client-joining" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/21f12e1c-f086-4777-93c7-36e9cc337be2">
		<br><br>
  		Back on the Client-1 device, go to system settings and follow the steps demonstrated in the video below to assign the Client to the domain created earlier.
    		<br><br>

https://github.com/telkheir/implementing-active-directory/assets/145223639/8308f06a-2e22-469c-8ffa-d74e731ff2f1
		
Client-1 is now a member of the domain and you should be able to log in to the client's computer with an admin's credentials.
	</li>
 	<li><h3 id = "#tep_5">Allow all domain users access to Client-1</h3>
		The video below shows how to allow domain users to access the Client-1 virtual machine.
    		<br>

https://github.com/telkheir/implementing-active-directory/assets/145223639/bc798654-9669-4caf-8509-be231ef829fb

<br>
		To ensure Client-1 can be accessed by other domain users, we need to login as another user. To do this, I first logged into DC-1 as an admin and opened PowerShell ISE an admin to run the contents of this <a href = "https://github.com/telkheir/implementing-active-directory/blob/main/create_users.ps1">script</a>, which generated 100 users and placed them in the Employees Organizational Unit created in <a href = "#step_3">step 3</a>. I then selected one of the user credentials to use to login to Client-1.
  		<br><br>
  		<img width="854" alt="ps-user-generate" src="https://github.com/telkheir/implementing-active-directory/assets/145223639/9b364021-d33e-4e4d-900e-58f3fba4cab3">
	</li>

</ol>
