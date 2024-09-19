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
I created a diagram to logically map out our lab's layout and hardware requirements. This diagram will help us understand data flow from one system to another and how all the components fit together.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/09adaac6904e3362fb7bdfa67ee51e4adc24822f/Images/Diagram.png" width="700" />
</p>

I've set up a virtual environment with Windows 10 as the Target PC, Kali Linux as the attacker machine, Windows Server 2022 for Active Directory, and Ubuntu Server to host my Splunk server. I’m installing them one at a time to conserve resources and avoid overwhelming the host machine.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/3759cda65f9b6534ce1b2e2dca282c0b9c60da88/Images/VM%20state.PNG" width="700" />
</p>

I've enhanced the Ubuntu Server's capabilities by increasing its memory to 4GB, while the other virtual machines operate with 2GB. This will allow it to effectively ingest data from Splunk forwarders and Sysmon logs, as well as run searches.

<div align="center">
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/cf3a223e9e8f24ae5909f9010823590c3649cece/Images/splunk%20operates%20at%204gigs.PNG" width="350" />
  <img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/cf3a223e9e8f24ae5909f9010823590c3649cece/Images/windows%20operates%20at%202gigs.PNG" width="365" />
</div>

I want to ensure that our virtual machine's network settings are configured to use a NAT network with a name that will be
used to identify them (I named it Active Directory Project). This will allow our virtual machines to communicate with each other while still having internet access. Next we change network settings to NAT network in all our virtual machines.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/d3439f2108a562b3f7c2cfa801dc28aa387079f3/Images/chanigng%20our%20network%20setting%20to%20NAT%20network.PNG" width="700" />
</p>

Next, we set a static IP address of our Ubuntu VM by editing the 50-cloud-init.yaml file using the nano editor and apply the changes using the 'sudo netplan apply' command. We then ping google.com (8.8.8.8 from our image below) to confirm that there is a connection.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/10773c7931572333b36a79230b7534528fb3d7d8/Images/splunk%20configuration.PNG" width="700" />
</p>

I then install Splunk on Ubuntu: Download the appropriate Debian package for Splunk, Configure shared folders in VirtualBox for easy file access and create a directory for shared files through the commandline. ie. mkdir share. We will run the following command to mount our shared folder onto our directory called share. To do that, we type in the command sudo mount -t vboxsf -o uid=1000,gid=1000 <shared_folder_name> /path/to/share.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/f6facaacfda88ccb7f295fad51dd93458932225d/Images/navigating%20to%20our%20shared%20folder.png" width="700" />
</p>

I'll type ls -la to display all files and hidden directories, and we'll see that our share is highlighted. Let's change into that share by typing cd [share-name]. Then, we can type ls -la again to list all files in that directory, including our Splunk installer. I installed splunk by typing sudo dpkg -i <splunk_installer.deb>

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/018c83aa47ba5f1461df54963a0c141d7c9243fd/Images/we%20see%20our%20splunk%20installer%20in%20the%20share%20%20directory.PNG" width="700" />
</p>

Once the installation is complete we resolve missing dependancies by runnign this command: sudo apt-get update && sudo apt-get upgrade, start the Splunk service with this command: sudo /opt/splunk/bin/splunk start. You can now access the Splunk web interface by navigating to http://192.168.10.10:8000 (from our layout diagram) in your web browser.

<p align="center">
<img src="https://github.com/NgethaWachira/Setting-up-a-Domain-Environment/blob/ab9456ffe3d6110e936199e0237106cff83d2cc0/Images/we%20open%20our%20splunk%20server%20from%20our%20windows%20target%20pc%20on%20port%208000.PNG" width="700" />
</p>


