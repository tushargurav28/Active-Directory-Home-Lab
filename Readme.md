# Active-Directory-Home-lab
### Overview 

We will be creating a lab environment utilizing several Windows 10 VMs along with Windows Server 2022. Windows Server 2022 will be used as our domain controller, which will allow for authentication and authorization for users that we establish within AD. The overall goal is to get acquainted with Active Directory, understanding what it provides for us and how it works. Active Directory is widely used in Windows domain enterprises, you will need to understand it at some point in your IT career.


### Lab Specs & Notes

Depending on your specs, and how many VMs you would like to have, you can upscale this lab as much as you want. For my purpose, I will only be utilizing 2 Windows 10 hosts along with Windows Server 2022. This will provide the core elements necessary to mimic a traditional on-premises enterprise network, despite the lack of hosts. You have the ability to add additional systems later on if you choose.

#### Host PC Specs:

* BlackArch   
* AMD Ryzen 7  2700x
* 16 GB RAM
* 1 TB SSD


##### Virtual Machine Specs: • Active Directory - 

Windows Server 2022 (1 CPU, 2 GB RAM) 
https://www.microsoft.com/en-us/windows-server

Workstation 1  Windows 10 Enterprise (1 CPU, 2 GB RAM) 
https://www.microsoft.com/en-us/software-download/windows10

Workstation 2 (optional) - Windows 10 Enterprise (1 CPU, 2 GB RAM)



# ![[Pasted image 20240923065902.png]]


##### Other Notes

* I am using VMware Workstation 17 Pro as my hypervisor, but there are plenty of free options that work just as well. I recommend VirtualBox as it has a lot of the same features as VMware and will work just fine for this lab. 
* All the steps below will be roughly the same for every hypervisor with the exception of buttons being in different locations. If you have trouble locating what I am referring to, do a google search. There is lots of documentation for all the popular hypervisors. 
* IMPORTANT: You need to make sure that you have virtualization enabled in your BIOS. If you do not have it enabled, you will not be able to start the VMs. To enable it, google your laptop or motherboard model depending on if you have a desktop and search “how to enable virtualization in x bios.” (x being your motherboard or laptop model)



##### Configuring Your Hypervisor 
1. After you have downloaded and installed your hypervisor, boot it up. I will be using VirtualBox for my demonstration images for configuring the VMs as my VMware installation is already populated with VMs.
You will be greeted with this (or similar):


# ![[Pasted image 20240923070914.png]]


![[Pasted image 20240923071005.png]]


2. We will begin by installing Windows 10 Enterprise. Here is the download link if you haven’t already got it: https://www.microsoft.com/en-us/evalcenter/downloadwindows-10-enterprise 

Choose your respective language and click the 64-bit edition, the download will start immediately.

![[Pasted image 20240923071121.png]]

3. Now that you have downloaded the ISO, we can set up the VM. Head over to your respective hypervisor, there will be a button to “Add a Virtual Machine” or similar. For the case of Virtual Box, click “New.”

![[Pasted image 20240923071224.png]]

4. After clicking “New,” a box will pop up. In the image below, I’ve included all the settings you should have for this first page. Feel free to choose whatever naming scheme you would like for your VMs. 

IMPORTANT: Do not add the ISO, we will add it once we have configured the Virtual Machine. If you add it in the initial setup, you will not be able to boot into the installation.



5. After you input the details above, click “Next.” We will be allocating 2 GBs of RAM, and 1 CPU. Depending on your system, you can allocate more RAM or CPUs. I do not recommend going below 2 gigs of RAM



![[Pasted image 20240923071356.png]]


6. Now, we will be creating a virtual hard disk. The minimum disk space for Windows 10 is 16 GBs, but I will be allocating 30. Feel free to add more or less, just do not go below 16 gigs.


![[Pasted image 20240923071441.png]]


7. Depending on your hypervisor, you should be greeted with a summary screen. Hit finish. Your new VM will populate on the left. Double click the new icon for your VM and let it start. Once it starts you will be greeted with a prompt to add a “DVD.” This is when we add our ISO.


![[Pasted image 20240923071522.png]]

Click the down arrow and click “other,” your file browser will pop up. Navigate to the folder that you downloaded your Windows 10 ISO to and select it. Once it is selected, click “Mount and Retry Boot” in the bottom right. 

8. Once the VM reboots, Windows 10 Setup will load. Click next on the initial screen unless you want to change the language, time and currency format, or keyboard layout.

![[Pasted image 20240923071610.png]]

9. Accept the license terms and select “Custom: Install Windows only.”

![[Pasted image 20240923071644.png]]

10. Click next to choose the default virtual drive that we created earlier in the initial VM setup.


![[Pasted image 20240923071721.png]]

11. The OS will now install, it may take some time depending on the specs of your PC.


##### Windows Server 2022 Installation

While we wait for our Windows 10 installation to finish, we can start installing Windows Server 2022. All the steps above will be the same, the only difference is that you will choose Windows Server 2022 ISO instead of the Windows 10 one as well as the operating system. More details below.

Here are the steps to download the ISO:

1. Head over to this link: https://www.microsoft.com/en-us/windows-server There will be a “Try Windows Server now” button. Once you click that, there will be another like to “Download free trial” of Windows Server 2022. After clicking that link, it will bring you to a page with the ISO download.


![[Pasted image 20240923071847.png]]

After filling out some details, you can download the ISO

2. As I said before, all the steps will be the same as our first installation. Instead of choosing Windows 10 for the version when creating the VM, you will choose Windows 2022 (64- bit).


![[Pasted image 20240923071923.png]]


3. After you boot into the installation, you will get to a screen that asks you which operating system you want to install. Choose “Windows Server 2022 Standard Evaluation (Desktop Experience)

![[Pasted image 20240923072006.png]]

4. Refer to our first installation of Windows 10 if you forgot any of the steps, and let the OS install.


##### Miscellaneous Notes & Common Installation Issues

* We will configure the accounts later in this guide, if your installations are complete, continue below
*  If any of your installations fail at some point, you may not have configured the VM with enough disk space. Make sure you are allocating a minimum of 16 gigs.
* If you get an error about windows being unable to find the license when booting up the VM, that means you installed the ISO during the initial setup screen. Delete the VM, restart and do not install the ISO until you boot it up and it prompts you to select a DVD.


Configuring Our Virtual Network

We will now configure the isolated network that our VMs will be on. This is an important step as this is what allows us to use AD with our other VMs. 

1. In the upper left-hand corner of VirtualBox, click File > Tools > Network Manager. Once you are in the Network Manager, head over to “NAT Networks.”

![[Pasted image 20240923073608.png]]


2. Copy the above settings. If you do not have a NatNetwork already, click create, and then fill in all the details from the image. Make sure DHCP is enabled. 
3. Now that we have our NatNetwork created, we are going put our VMs on it. The steps will be the same for each VM. 
4. Right click one of your VMs and click “settings.” Once you are in the settings panel, click on “Network” and change “Attached to:” to the NAT Network.

![[Pasted image 20240923073723.png]]

5. Repeat this step for your remaining VMs. Now you are all set! Your VMs are on their own isolated network.


##### Account Creation

We will now head back over to our VMs and create our accounts. We are going to start with our Windows 10 host(s).

1. If you have not already, go through the initial prompts. Choose your region, keyboard layout, and make your way to the Account screen. In the bottom left, you are going to click “Domain Join Instead.

![[Pasted image 20240923073843.png]]

2. Use whatever name suits your fancy and click next. (I chose tony for Tony Stark )

![[Pasted image 20240923073923.png]]

3. Enter in a password and fill out security questions. I disabled all the privacy settings, but it will not affect anything if you choose to leave them on. 
4. Now repeat the process for your other windows hosts.

NOTE: For Windows Server, the only thing you need to do is enter a password.

##### Configuring Our Hosts

After all that setup, we can finally get into the meat and potatoes. But there is one more thing we need to do in order for us to join our Windows hosts to the domain. We need to configure our Windows 10 hosts to use our Active Directory servers IP address as their DNS server.

1. On each of your Windows 10 hosts, navigate to settings > Network & Internet > change adapter options. Right click on your adapter and click “Properties.”


![[Pasted image 20240923074118.png]]


2. On the properties page, click “Internet Protocol Version 4 (TCP/IPv4), and click “Properties.”

![[Pasted image 20240923074200.png]]

3. Now click “Use the following DNS server addresses.” And now we are going to head back over to our Windows Server 2022 VM. 
4. Once you navigate back to you server VM, click the search bar and type “cmd.” 
5. In the command prompt type “ipconfig” 
6. Take note of the IPv4 address that is displayed on your server

![[Pasted image 20240923074254.png]]

7. Now head back over to your windows 10 host and enter the IPv4 address from our server and enter it in the “Preferred DNS server” field.

![[Pasted image 20240923074332.png]]

8. Repeat this process for however many other Windows 10 hosts you setup.

NOTE: You DO NOT need to do this for the server.

##### Active Directory Setup at Last

Okay, now I am serious, we can finally get into the meat and potatoes of this whole lab. Configuring Active Directory. 
\
1. If you were observant, you should have noticed that an application called “Server Manager” booted when you first logged into your administrator account. If it did not, you can search for it in the search bar. From this page, we are going to install Active Directory. Click “Add roles and features.”

![[Pasted image 20240923080305.png]]

2. The wizard will pop up, from here we are going to click next until we get to the “Server roles” page. On this page, we are going to select “Active Directory Domain Services.”


![[Pasted image 20240923080338.png]]

3. Once you check the box, a window will pop up. From here, click “Add Features.” 
4. Click next and leave the features default. 
5. You will get to the confirmation page, click install in the bottom right and let feature installation finish. 
6. Active Directory is now installed, in the top right corner, click the flag with the yellow exclamation point triangle, and click “promote this server to a domain controller.”

![[Pasted image 20240923080433.png]]

7. A new window will pop up. We need to create a new forest. Click the “Add a new forest” button and specify a root domain name. I will call mine “iron.local” but you can name yours whatever you like.

MAKE SURE YOU ADD THE .local AFTER WHATEVER YOU NAMED IT

![[Pasted image 20240923080519.png]]

8. Click next and on the Domain Controller Options page, specify a DSRM password. Leave everything else default.


![[Pasted image 20240923082500.png]]

9. Click next, not selecting the “Create DNS Delegation” button until you reach the “Additional Options” page. On this page, the NETBIOS domain name will be generated. It should be whatever you put for the root domain name without the .local at the end.


![[Pasted image 20240923082525.png]]

10. Click next until you reach the prerequisites check page, leaving everything default. On the prerequisite check page, it will check the system to make sure it can successfully install AD.


![[Pasted image 20240923082548.png]]


11. Once the check is completed, click install. After the installation is completed, you will need to reboot the server. It may take a while for the settings to apply.


##### Creating Users in Active Directory

Now that we have finished installing Active Directory, we can now create some users. 

1. In the top right of Server Manager, Navigate to Tools > Active Directory Users and Computers.

![[Pasted image 20240923083003.png]]

2. Click the arrow next to your domain and navigate to the users section.

![[Pasted image 20240923083040.png]]

3. In the users panel, right click and select New > User. A user object creation box will pop up.

![[Pasted image 20240923083116.png]]

4. Fill in whatever first and last name you would like. I will be doing “Tony Stark.” For the username, first initial and last name of your user and click next.

![[Pasted image 20240923083145.png]]

5. Specify a password for the user and uncheck the “User must change password at next logon” option. Select the “password never expires” option.

NOTE: these settings are just to make our lives easier. We are in an isolated lab environment, in a normal enterprise environment, you would not set the password to never expire, and you would keep the option selected for letting the user change the password on next login.

![[Pasted image 20240923083231.png]]

6. Click next and confirm that the details are correct on the summary screen. 
7. Congrats! You created your first user in AD. Repeat this process for however many host VMs you decided to use for your lab.

##### Joining Our Hosts to the Domain

At this point in the lab, you should have created the number of users respective to the amount of VMs you have. We will now join each VM to the domain.

1. Navigate to one of your Windows 10 hosts. 
2. In the search bar type “access work or school”

![[Pasted image 20240923083342.png]]


3. Once the window pops up, click “connect now.” Instead of entering a microsoft account, you are going to select “join this domain to a local Active Directory domain” in the bottom right. 

NOTE: If you configured your host’s preferred DNS server with your servers IP address, you should get a prompt to login. If not, refer to the “Configuring Our Hosts” section of the lab.

![[Pasted image 20240923083508.png]]

4. After selecting the local AD option, you will be prompted to join a domain. From here, you will type in the name that you specified for your root domain (i.e. example.local)


![[Pasted image 20240923083530.png]]

5. If you typed in your domain correctly, you will be asked to login. Choose one of the accounts that you created within the Active Directory users section from earlier.

![[Pasted image 20240923083807.png]]

6. If you specified a correct account, a box will pop up asking you to add an account. Leave the user account field default and make the account a “Standard User.

![[Pasted image 20240923083836.png]]


7. Go ahead and restart the pc when prompted. 
8. Repeat this process for each Windows 10 host you have.

##### Conclusion

Congratulations! You did it! You properly installed and configured Active Directory. The whole point of this lab was to get your feet wet with AD. Hopefully now you have a better understanding of how it works. Feel free to mess around a bit with it, explore, change some settings, configure some things. After all, that is the whole point of a home lab! 
I have lots of plans for future labs, some more stuff with Active Directory, configuring a SIEM, and doing some vulnerability scanning and exploitation, and some pfsense firewall stuff. I hope you found this useful, if you have any questions, feel free to reach out to me on LinkedIn.































































































