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

<h2>Windows Event Logs</h2>
In this first part, we will be opening a saved log by the title of <b>DLLHijack</b> to investigate for any malicious activities.
