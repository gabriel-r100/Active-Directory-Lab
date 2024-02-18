<h1>Active Directory Lab - Uploading Content</h1>

![ActiveDirectoryLab drawio](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/23668c33-5348-408e-b41f-88ba144404ad)

<h2>Introduction</h2>

In this lab, I install Windows Server 2019 on a virtual machine using Oracle VirtualBox, configure an two network adapters, add Active Directory Domain Services, Router Services, and DHCP Services.<br>
Additionally, we generate users using a list of names (text file) and PowerShell and add them to an Organizational Unit named `_USERS`<br>
Lastly we ensure that an endpoint (Windows 10 VM) can join our Active Directory domain.




<h2>Windows Server 2019</h2>

![0-2-ActiveDirectoryLab drawio](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/90dc3da3-ee00-4d75-95ef-f96af14e622a)




<details><summary><h3>Virtual Machine Configuration</h3></summary>
  1. We start off by downloading a Windows Server 2019 ISO file from Microsoft. (Look for 'Windows Server 2019')
  2. When configuring the virtual machine, ensure to configure two network adapters, one will be set to NAT and the other to internal.
     a. NAT will be the internet facing one
     b. Internal will only be able to communicate with our virtual network
     c. Optionally, you can add more vCPUs and/or additional vRAM if you system can support it, otherwise default configurations are ok.

   ![4-1-ServerVM-Overview](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/7931229f-96ab-491f-b089-62d4812565f9)

  3. Once you have configured the settings, you can launch the virtual machine (VM) and continue through the installation process.
   ![5-Server2019-Installation](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/9905cd04-9e67-4012-9aa0-3a55dd1cb96d)

</details>




<details><summary><h3>Network Adapter Configuration and System Name</h3></summary>
  1. Now that we have successfully installed Windows Server 2019, we can configure and rename the network adapters for our use case.<br>
  2. Near the bottom right you will find the network settings icon, we select `Change adapter options`<br>
  3. We can find our two network interfaces here, we want to double click on them and select `Details`<br>
  
   ![7-Finding-NICs-VM-GUI](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/8de89c4c-3b24-43ee-9b55-28b2065efcf3)

  4. The one with a `IPv4 Address` that begins with 169 is our internal interface that we will rename `_INTERNAL`
  5. The second interface with will rename `_INTERNET`
   ![8-Renaming-Server-NICs](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/8b832026-f8be-4095-ae09-1f977eee7781)
  6. On our internal facing interface, we will select `Properties` and assign the static IP address of `172.16.0.1`. Because this server will server as a router for our virtual network, it does not require a value for gateway.
   ![9-Assigning-Static-IP-Info-Internal-NIC](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/a20d66e7-1d4a-4b8d-ae68-4d215a0d5118)

  7. Right-clicking on the start menu will bring up a menu, select `System` in here you can find the `Rename this PC` option to rename this host.
![10-1-Renaming-System](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/668ae43f-4b6a-4554-8c5c-9574b753aa90)
![10-2-Renaming-System](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/b4a38d36-fd2e-497c-969c-8c89655e2b22)

</details>




<h2>Server Roles and Features</h2>

![0-3-ActiveDirectoryLab drawio](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/0bc09da1-6d67-478d-9859-1aaf35a5fbce)




<details><summary><h3>Installing Active Directory Domain Services</h3></summary>
1. We start on the Windows Server Dashboard, select the Add Roles and Features. <br>
  a. Installation Type: Role-based or feature-based installation <br>
  b. Server Selection: Here we can select this server <br>
  c. Server Roles: <b>Active Directory Domain Services</b> <br>
2. Once the Active Directory Service has been installed, we need to promote the server to a domain controller. <br>
  a. There will be a yellow flag on the top right, promote the server.<br>
  b. For this project we create a new forest as there was no existing domain.<br>
![11-Adding-New-Forest](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/b222addb-0332-4d50-8a8a-4a50bebf8f61)
3. We first make an Organizational Unit for our administrators.
![12-Creating-Organizational-Unit](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/009dfdc8-9e9d-4127-9d9a-b109c67f06bd)

4. Once created, we create our administrator account under the newly created OU.
![13-Adding-User-to-OU](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/226e71db-61c6-40a8-a39b-45e3a71e5f10)

5. Lastly, although our admin account was created, it still does not have administrator-level privileges. We can update it by right-clicking on our user, navigating to the "Member Of" tab, and adding "domain admins". This will let us sign in as our new user and continue the lab from a personal administrator account.
![14-Adding-User-to-Domain-Admins-Group](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/c7e84e3c-76ae-45d6-904c-4dc4a6366648)

</details>

<details><summary><h3>Enabling Router Services</h3></summary>
1. We'll start off similar to installing our Active Directory services. Under roles, the Routing services we are looking for fall under <b>Remote Access</b>. For Role services be sure to enable Routing.
![15-Adding-Remote-Access-Server-Role](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/3fbd9590-ed05-484d-ac71-2ce78da1c77f)
![16-Enabling-Routing](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/47a10548-cdce-4832-b76c-d84fd1737c3a)

2. Once installed, click on tools on the top right, select <b>Routing and Remote Access</b>.
![17-1-Configuring-NAT](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/6f877946-47fa-444b-8e7d-9b07340ea006)

3. We can right-click our domain controller to configure the NAT rule to allow our internal clients to connect to the internet via the domain controller (this Windows 2019 Server).
![17-2-Configuring-NAT](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/48a27d09-5f0b-4fc8-8540-4acd03568355)
![17-3-Configuring-NAT](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/62e41872-7bef-4cd4-9aff-c179d33e6bee)

</details>

<details><summary><h3>Adding DHCP Services</h3></summary>
1. We once again start by adding the DHCP role on our server.<br>
2. Once installed, we click on tools (top right) and select DHCP to configure our DHCP scope.<br>
3. First we give our scope a name, we can either name it for a particular function. For this lab I chose to name it after the scope of IPs we will be handing out. `172.16.0.100-200`<br>

![18-1-Configuring-DHCP-Scope](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/32433c19-5290-44d3-8d3a-b02d80092372)

4. We will then need to define the scope in the configuration.<br>
![18-2-Configuring-DHCP-Scope](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/76f36666-a7f6-433b-831e-ad9f461d1d70)

5. In the next prompt, we can configure the length of the leases we are handing out. If we are working at a coffee shop, we are much more likely to go with a lower lease time to maintain a smaller scope of IPs. In this lab, I emulate a small office so I choose to keep it at 8 days lease time.<br>
![18-3-Configuring-DHCP-Scope](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/fd103f3d-7a1c-4aa0-ba04-1deb0d9a6c48)

6. Once configured, we can see the leases on the same DHCP window. Below is where to check, but currently I have not configured a client.<br>
![19-Viewing-DHCP-Leases](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/b7397165-ad44-475b-af0b-812e6e3e9610)

</details>




<h2>Creating Active Directory Accounts Using PowerShell</h2>

![0-4-ActiveDirectoryLab drawio](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/09643b82-8a02-4bcc-8100-0ed8425b967c)





<h2>Connecting a Windows 10 Client to Our Domain</h2>

![0-5-ActiveDirectoryLab drawio](https://github.com/gabriel-r100/Active-Directory-Lab/assets/55646808/c54993e6-f66c-40a2-a5c0-bc4d19646eae)




<details><summary><h3>Virtual Machine Configuration</h3></summary>
</details>

<details><summary><h3>Renaming Endpoint and Joining Domain</h3></summary>
</details>
