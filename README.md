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
  <img src="https://img.shields.io/badge/-Microsoft%20Defender-4D4D4D?&style=for-the-badge&logo=microsoft&logoColor=white" alt="Microsoft Defender">
</div>

## Steps





