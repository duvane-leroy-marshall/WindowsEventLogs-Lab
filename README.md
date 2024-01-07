# WindowsEventLogs-Lab
A HackTheBox module that covers the exploration of Windows Event Logs and its significance in uncovering suspicious activities.

<h1>Windows Event Logs & Finding Evil</h1>

[Module Link](https://academy.hackthebox.com/course/preview/windows-event-logs--finding-evil)

<h2>Description</h2>
This module focuses on exploring Windows Event Logs and their role in identifying suspicious activities.

This lab covers the following: 

- <b>A dive into the intricacies of Windows Event Logs, examining their anatomy and highlighting the most valuable logs for investigative purposes</b>
- <b>The utilization of Sysmon and Event Logs to detect and analyze malicious activities</b>
- <b>The Process of install or updating Sysmon</b>
- <b>Real-world examples of detection, including identifying DLL hijacking, unmanaged Powershell/C-Sharp injection, and credential dumping</b>
- <b>A delve into Event Tracing for Windows and its architecture</b>
- <b>ETW-based detection examples like identifying unusual parent-child relationships, and malicious .NET assembly loading</b>
- <b>The Get-WinEvent cmdlet for streamlined analysis of Windows Event Logs</b>

<h2>Tools Used</h2>

- <b>Windows Event Viewer</b> 
- <b>Sysmon</b>
- <b>Windows Command Line</b>
- <b>Powershell</b>

<h2>Environments Used </h2>

- <b>HackTheBox Pwnbox Linux</b>
- <b>Windows 10 Virtual Machine</b>

<h2>Guided Lab: Analyzing Evil With Sysmon & Event Logs</h2>
In order to use Sysmon, we must first download it. We can do this by downloading it from the official Microsoft documentation website. Once downloaded, we can install it by using the command prompt as an administrator.
<br />
<br />

<p align="center">
Installing Sysmon: <br/>
<img src="https://i.imgur.com/yMGVaQG.jpg" height="80%" width="80%" alt="Sysmon Install"/>
<br />
<br />

<h2>Configuring Sysmon for DLL Hijacks</h2>
To detect a DLL hijack, we need to focus on Sysmon's <b>event ID 7</b>, which corresponds to module load events. To achieve this, we need to modfiy the <b>sysmonconfig-export.xml</b> file. There we will make our modifications and update the file through the command prompt.
<br />
<br />

<p align="center">
The Sysmon Configuration File: <br/>
<img src="https://i.imgur.com/O6zXMBa.jpg" height="80%" width="80%" alt="Sysmon Config File"/>
<br />
<br />
Sysmon Event ID 7 Settings: <br/>
<img src="https://i.imgur.com/aiKiF02.jpg" height="80%" width="80%" alt="Sysmon Event ID 7 Include"/>
<br />
<b>Here we will have to change the "include" to "exclude". This ensures that nothing is excluded, allowing us to capture all the necessary data.</b>
<br />
The Change: <br/>
<img src="https://i.imgur.com/HAJ2ryZ.jpg" height="80%" width="80%" alt="Sysmon Event ID 7 Exclude"/>
<br />
<br />
Updating the Configuration File: <br/>
<img src="https://i.imgur.com/lL8w9fw.jpg" height="80%" width="80%" alt="Update Sysmon Config File"/>

<h2>Detection Example 1: Detecting DLL Hijacking</h2>
