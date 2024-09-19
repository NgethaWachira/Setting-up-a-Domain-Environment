# Setting-up-a-Domain-Environment

## Objective
The goal is to establish a fully functional on-premise domain environment, which involves installing and 
configuring Active Directory, Splunk, Windows machines, and Kali Linux within a virtual environment.

### Skills Learned
- Creating a diagram to logically plan a lab environment with a focus on data flow and how components interact with each other.
- Installing and configuring Windows Server 2022, Windows 10, Kali Linux, Ubuntu Server in a virtual environment and their static 
  IP addresses keeping in mind the best resources to allocate depending on the expected workload. 
- Setting up NAT networks for Windows Server 2022, Windows 10, Kali Linux, Ubuntu Server to enable communication between the devices to 
  allow for communication between them while at the same time being connected to the internet.
- Installing and configuring Active Directory Domain Services (AD DS), promoting the server to a Domain Controller (DC) and creating 
  user accounts from organizational units.
- Installing and configuring Sysmon for monitoring and logging system events and setting up Splunk Universal Forwarder to 
  collect and forward logs to the Spunk server.
- Utilizing tools like Atomic Red Team to simulate attacks and generate telemetry, which was forwarded to the Splunk server, 
  where I queried the data using event ID logs.
- Running brute-force attacks from Kali with tools like Crowbar and analyzing the resulting logs in Splunk.
- Analyzing logs in Splunk to detect anomalies and security events, such as brute force attempts.
- Diagnosing connectivity issues and ensuring that services are running correctly after configuration changes.
- Keeping track of configuration changes and settings, ensuring proper documentation for installation processes and troubleshooting.

### Tools Used
<div>
  <img src="https://img.shields.io/badge/-Active%20Directory%20Domain%20Services-7300e6?&style=for-the-badge&logo=microsoft&logoColor=white" alt="Active Directory Domain Services">
  <img src="https://img.shields.io/badge/-Sysmon-00cc99?&style=for-the-badge&logo=windows&logoColor=white" alt="Sysmon">
  <img src="https://img.shields.io/badge/-Splunk-3d5c5c?&style=for-the-badge&logo=splunk&logoColor=white" alt="Splunk">
  <img src="https://img.shields.io/badge/-Splunk%20Universal%20Forwarder-cccc00?&style=for-the-badge&logo=splunk&logoColor=white" alt="Splunk Universal Forwarder">
  <img src="https://img.shields.io/badge/-Atomic%20Red%20Team-FF5722?&style=for-the-badge&logo=red&logoColor=white" alt="Atomic Red Team">
  <img src="https://img.shields.io/badge/-VirtualBox-8a8a5c?&style=for-the-badge&logo=VirtualBox&logoColor=white" alt="VirtualBox">
  <img src="https://img.shields.io/badge/-Kali%20Linux-ff0000?&style=for-the-badge&logo=kali-linux&logoColor=white" alt="Kali Linux">
  <img src="https://img.shields.io/badge/-Windows%20Server%202022-0078D4?&style=for-the-badge&logo=microsoft&logoColor=white" alt="Windows Server 2022">
  <img src="https://img.shields.io/badge/-Ubuntu%20Server-994d00?&style=for-the-badge&logo=ubuntu&logoColor=white" alt="Ubuntu Server">
  <img src="https://img.shields.io/badge/-Crowbar-009900?&style=for-the-badge&logo=crowbar&logoColor=white" alt="Crowbar">
  <img src="https://img.shields.io/badge/-Microsoft%20Defender-800080?&style=for-the-badge&logo=microsoft&logoColor=white" alt="Microsoft Defender">
</div>

## Steps
- I created a diagram to logically map out our lab's layout and hardware requirements. This diagram will help us understand data flow from one system to another and how all the components fit together.
  - Layout Diagram

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/09adaac6904e3362fb7bdfa67ee51e4adc24822f/Images/Diagram.png" width="700" />
</p>

- I've set up a virtual environment with Windows 10 as the Target PC, Kali Linux as the attacker machine, Windows Server 2022 for Active Directory, and Ubuntu Server to host my Splunk server. I’m installing them one at a time to conserve resources and avoid overwhelming the host machine.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/3759cda65f9b6534ce1b2e2dca282c0b9c60da88/Images/VM%20state.PNG" width="700" />
</p>

- I've enhanced the Ubuntu Server's capabilities by increasing its memory to 4GB, while the other virtual machines operate with 2GB. This will allow it to effectively ingest data from Splunk forwarders and Sysmon logs, as well as run searches.

<div align="center">
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/cf3a223e9e8f24ae5909f9010823590c3649cece/Images/splunk%20operates%20at%204gigs.PNG" width="350" />
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/cf3a223e9e8f24ae5909f9010823590c3649cece/Images/windows%20operates%20at%202gigs.PNG" width="365" />
</div>

- I want to ensure that our virtual machine's network settings are configured to use a NAT network with a name that will be
used to identify them (I named it Active Directory Project). This will allow our virtual machines to communicate with each other while still having internet access. Next we change network settings to NAT network in all our virtual machines.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/d3439f2108a562b3f7c2cfa801dc28aa387079f3/Images/chanigng%20our%20network%20setting%20to%20NAT%20network.PNG" width="700" />
</p>

- Next, we set a static IP address of our Ubuntu VM by editing the `50-cloud-init.yaml` file using the nano editor and apply the changes using the: `sudo netplan apply` command. We then ping google.com (8.8.8.8 from our image below) to confirm that there is a connection.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/10773c7931572333b36a79230b7534528fb3d7d8/Images/splunk%20configuration.PNG" width="700" />
</p>

- I then install Splunk on Ubuntu: Download the appropriate Debian package for Splunk, Configure shared folders in VirtualBox for easy file access and create a directory for shared files through the commandline: `mkdir share`. We will run the following command to mount our shared folder onto our directory called share. To do that, we type in the command: `sudo mount -t vboxsf -o uid=1000,gid=1000 <shared_folder_name> /path/to/share`.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/f6facaacfda88ccb7f295fad51dd93458932225d/Images/navigating%20to%20our%20shared%20folder.png" width="700" />
</p>

- I'll type ls -la to display all files and hidden directories, and we'll see that our share is highlighted. Let's change into that share by typing cd `share-name`. Then, we can type: `ls -la` again to list all files in that directory, including our Splunk installer. I installed splunk by typing: `sudo dpkg -i <splunk_installer.deb>`

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/018c83aa47ba5f1461df54963a0c141d7c9243fd/Images/we%20see%20our%20splunk%20installer%20in%20the%20share%20%20directory.PNG" width="700" />
</p>

- Once the installation is complete we resolve missing dependancies by running this command: `sudo apt-get update && sudo apt-get upgrade`, start the Splunk service with this command: `sudo /opt/splunk/bin/splunk start`. You can now access the Splunk web interface by navigating to `http://192.168.10.10:8000` (from our layout diagram) in your web browser.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/ab9456ffe3d6110e936199e0237106cff83d2cc0/Images/we%20open%20our%20splunk%20server%20from%20our%20windows%20target%20pc%20on%20port%208000.PNG" width="700" />
</p>

- Next, I installed Sysmon and the Splunk Universal Forwarder on both the Target PC and the Windows Server. I then configured the Universal Forwarder in the settings tab, specifying the IP address and port of our Splunk server as the destination for data transmission. Create an inputs.conf file to define what data to send to the Splunk server. `Inputs.conf` file is located in the: `C:\Users\SplunkUniversalForwarder\etc\system\local\` folder.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/dca00cb0f2732a4b2ea445bd18e11394807c5fb2/Images/enabling%20our%20splunk%20server%20to%20receive%20the%20data.PNG" width="700" />
</p>

- After making changes to the configuration files, restart the Splunk Universal Forwarder from the Services application in Windows to ensure the changes take effect.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/6a649599122244d940f3fbfb0540ca88d740b2cd/Images/restarting%20splunk%20service%20after%20updating%20the%20inputs.conf%20file.PNG" width="700" />
</p>

- Log in to Splunk, navigate to the indexes, and create an index named `endpoint` as defined in the `inputs.conf` file. This will help organize and categorize incoming data for easier management and retrieval. To do this, go to Settings in the top right corner, click on Indexes, and then select New Index. Once created, it will appear in the list of indexes.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/1ec1923380108c8c9696655411ab4f23b0148698/Images/new%20endpoint%20index%20created.PNG" width="700" />
</p>

- Verify that data from the Target machine is being received by Splunk by navigating to the Search & Reporting app. In the search bar, type `index=endpoint`, ensure the time range is set to our period of interest, and disable event sampling. Check the displayed data for confirmation.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/6e719a987bca762b512c642cf327a4a80b2af487/Images/confirming%20we%20have%20setup%20splunk%20correctly.PNG" width="700" />
</p>

- I configured the Windows server with a static IP address of `192.168.10.7`, following our layout diagram. By pinging the Splunk server at `192.168.10.10`, we can confirm that the Windows server is properly connected to the network. A successful ping indicates that both devices can communicate effectively.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/1cf9f7ab28225aceee8b27047c1db7b5c916cf34/Images/since%20we%20can%20ping%20our%20splunk%20server%20we%20assume%20there%20is%20connectivity%20btwn%20the%20computers.PNG" width="700" />
</p>

- Open Server Manager on your server, click on Manage, and select Add Roles and Features. In the wizard, proceed to the Server Roles section and check the box for Active Directory Domain Services. Continue through the next pages, reviewing the features and settings, until you reach the Install button. Click Install and wait for the installation to complete. Once done, click Close.

<div align="center">
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/363df349b1d9eb2571172bdd7147c98a42bd9601/Images/adding%20roles%20on%20server%20manager.PNG" width="350" />
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/363df349b1d9eb2571172bdd7147c98a42bd9601/Images/finished%20installation.PNG" width="365" />
</div>

- On Server Manager, click on the notification flag and select Promote this server to a domain controller. Choose whether to add a new forest and enter your root domain name. Carefully configure the Domain Controller Options. If you receive warnings about DNS, you can ignore them and proceed with the installation. Once the setup is complete, the server will automatically restart. 

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/5dd48451f539ccd89f522823c5f060ca4f795637/Images/promoting%20the%20server%20to%20a%20domain%20controller.PNG" width="700" />
</p>

- After restarting, Upon logging back into our server, you will see our domain name followed by a backslash, indicating that Active Directory Domain Services (AD DS) has been successfully installed and the server has been promoted to a domain controller. The next step is to start creating user accounts. Users are created in organizational units such as HR, Finance, IT etc. So we create two organizational units, HR and IT and within the two organizational unit we add one user to each.

<div align="center">
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/500e520c53a56c0c170354d45ca6e2f9ec1b591c/Images/created%202%20organizarional%20units%20and%202%20users.PNG" width="365" />
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/500e520c53a56c0c170354d45ca6e2f9ec1b591c/Images/within%20the%20new%20IT%20unit%20we%20create%20a%20new%20user%20called%20james%20rain.PNG" width="350" />
</div>

- Now that we have set up Active Directory and our server is functioning as a domain controller, we will go to our Windows target machine to join it to the newly created domain, jay.project, and authenticate using the James Rain (jrain username) account created belong to the IT department.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/cc785b472bed9ace12b00080b8b071b6c61943e4/Images/new%20user%20connected%20to%20our%20domain%20controller.PNG" width="700" />
</p>

- Once you have established the connection, log out of the new user account and log back in using the administrator account, which has the necessary permissions. Then, change the preferred DNS settings from Google’s `8.8.8.8` to our domain controller at `192.168.10.7`. 

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/982a8962a03b733f0e25eace838cedea8b1b2226/Images/changing%20DNS%20to%20our%20domain%20controller.PNG" width="700" />
</p>

- Ensure that you take snapshots of all your virtual machines. This way, if something goes wrong, you can easily restore them to a known good state.

- Next, we set up Atomic Red Team (ART) using PowerShell with administrative privileges. To enable the necessary permissions, run the command `Set-ExecutionPolicy Bypass CurrentUser` and confirm by pressing 'Y' for yes. ART is used for generating alerts in Splunk by simulating attack techniques to test the detection capabilities of your security monitoring setup.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/61e507c950903bc70d00b5f067290abb7b883888/Images/Atomic%20finished%20installing.PNG" width="700" />
</p>

- On the Linux machine, we will set a static IP address of `192.168.10.250` as specified in our layout diagram.
To verify connectivity, first ping google.com. If that is successful, we can then ping our Splunk server 
and `192.168.10.10` confirm connectivity, there needs connectivity in order to push alerts.

<div align="center">
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/469e1deefbc4c2311ca1eff1a654c3b137a199bf/Images/editing%20kali%20ip%20address.PNG" width="365" />
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/e48a50031a32eeb167d86289262169e91ef8cf1d/Images/ip%20changed.PNG" width="360" />
</div>

- I created a new directory called 'AD-project' on our desktop, where all the files we create and use will be stored. We will be using a tool called `Crowbar` we will use to perform Brute Force attacks, we can target either our domain controller or the target machine. After installation, we will use a popular word list called `rockyou` that comes with Kali Linux. Let's copy this file to our 'AD-project' folder and use the first 20 lines. In order for this to work we will store our real password in the `rockyou` text file. Our goal is to simulate the attack in order to see the alert in Splunk.

<div align="center">
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/066e8c2f840bb59ae31262df7272aa46fbfe6b6f/Images/follow%20instructions.PNG" width="350" />
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/066e8c2f840bb59ae31262df7272aa46fbfe6b6f/Images/we%20only%20need%20the%20first%2020passwords.PNG" width="350" />
</div>

- Before we launch the attack, let's go to our Windows target machine and enable Remote Desktop. We also need to add the two users `jrain` and `wfisk` (usernames) we created in Server Manager.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/5b4a1bdfc5d1bdfc9b5b88f70a35785620726f89/Images/adding%20created%20users%20and%20enabling%20RDP%20on%20our%20target%20machine.PNG" width="700" />
</p>

- On the Kali Linux machine, I'll run the command: `crowbar -b rdp -U jrain -C password.txt -s 192.168.10.100/32`. Once we execute Crowbar, it will begin testing all the passwords listed in the `password.txt` file. Eventually, we will see a successful RDP connection with the username `jrain`.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/73b67fb56d17a5b92107c090c40ca04f2433dd7f/Images/we%20ran%20our%20command%20using%20crowbar%20tool.PNG" width="700" />
</p>

- Now, let's check Splunk to review the telemetry generated, where we see 20 failed login attempts for the `jrain` account, all occurring nearly simultaneously, indicating possible brute force activity.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/2e9e0989da03a870effabfa2836f34e9f0846955/Images/events%20happened%20at%20the%20same%20time%20shows%20bruteforce%20activity.PNG" width="700" />
</p>

Everything is working as i had hoped. Simulating attacks is essential for strengthening security posture, improving detection capabilities, and preparing teams for real-world scenarios. Cheers.
