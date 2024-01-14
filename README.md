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

<h2>Guided Lab Part 1: Analyzing Evil With Sysmon & Event Logs</h2>
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
With the modified Sysmon configuration, we can start observing Sysmon's event ID 7. To view these events, we navigate to the Event Viewer along this path: <b>Applications and Services -> Microsoft -> Windows -> Sysmon</b>.
<br />
<br />
We will be using <b>calc.exe</b> and <b>WININET.dll</b> as an example. To simplify the process, we can utilize Stephen Fewer's "hello world" <b>reflective DLL</b>. We will do this by renaming <b>reflective_dll.x64.dll</b> to WININET.dll, copying calc.exe from <b>C:\Windows\System32</b> along with WININET.dll to a writable directory (such as the Desktop folder), and executing calc.exe, we achieve success. Instead of the Calculator application, a MessageBox is displayed.
<br />
<br />

<p align="center">
The Reflective DLL Injection: <br/>
<img src="https://i.imgur.com/FPiruAC.jpg" height="80%" width="80%" alt="Reflective DLL Injection"/>
<br />
<br />
Event Viewer Log Filter: <br/>
<img src="https://i.imgur.com/jgPmIaT.jpg" height="80%" width="80%" alt="Event Viwer Filter"/>
<br />
<b>Next we will analyze the hijack in our Event Viewer. First we will filter the event logs to focus on event ID 7. We do this by clicking "Filter Current Log..."</b>
<br />
<br />
The Event Viewer Find Function: <br/>
<img src="https://i.imgur.com/FW692u4.jpg" height="80%" width="80%" alt="Find"/>
<br />
<b>Since we know the injection came from clicking the calc.exe, we can use the "Find..." feature to search for all events associated with the hijack.</b>
<br />
<br />
Our Culprit: <br/>
<img src="https://i.imgur.com/vlaC7M3.jpg height="80%" width="80%" alt="Calc Location"/>
<br />
<img src="https://i.imgur.com/SJWr3Bz.jpg" height="80%" width="80%" alt="Calc Signature"/>
<br />
<b>The above images show us a clearer picture on the reflective dll injections. Here, we can examine three indicators of compromise (IOCs):
  
  - "calc.exe", originally located in System32, should not be found in a writable directory. Therefore, a copy of "calc.exe" in a writable directory serves as an IOC, as it should always reside in System32 or potentially Syswow64.
  - "WININET.dll", originally located in System32, should not be loaded outside of System32 by calc.exe. If instances of "WININET.dll" loading occur outside of System32 with "calc.exe" as the parent process, it indicates a DLL hijack within calc.exe. While caution is necessary when alerting on all instances of "WININET.dll" loading outside of System32 (as some applications may package specific DLL versions for stability), in the case of "calc.exe", we can confidently assert a hijack due to the DLL's unchanging name, which attackers cannot modify to evade detection.
  - The original "WININET.dll" is Microsoft-signed, while our injected DLL remains unsigned.</b>

<h2>Detection Example 2: Detecting Unmanaged PowerShell/C-Sharp Injection</h2>
C# is considered a "managed" language, meaning it requires a backend runtime to execute its code. As cybersecurity defenders, we can leverage knowledge to detect unusual C# injections or executions within our environment. To accomplish this, we can utilize a useful utility called <b>Process Hacker</b>.
<br />
<br />

<p align="center">
The Process Hacker: <br/>
<img src="https://i.imgur.com/zBrw8xD.jpg" height="80%" width="80%" alt="Process Hacker"/>
<br />
<br />
powershell.exe in Process Hacker: <br/>
<img src="https://i.imgur.com/R4C9qFG.jpg" height="80%" width="80%" alt="powershell.exe"/>
<br />
<b>In Process Hacker, we can sort the processes by name, and can identify what they do by their colour-coded distinctions. Notably, "powershell.exe", a managed process, is highlighted in green compared to other processes. Hovering over powershell.exe reveals the label "Process is managed (.NET)."</b>
<br />
<br />
Powershell Properties: <br/>
<img src="https://i.imgur.com/ElEmuC8.jpg" height="80%" width="80%" alt="Powershell Properties"/>
<br />
<b>We can examine the powershell.exe module loads by right-clicking on it, clicking "Properties", and navigating to "Modules". Here we can find revelant information. The presence of "Microsoft .NET Runtime...", clr.dll, and clrjit.dll should attract our attention. These two DLLs are used when C# code is ran as part of the runtime to execute the bytecode. If we observe these DLLs loaded in processes that typically do not require them, it suggets a potential execute-assembly or unmanaged PowerShell injection attack.</b>
<br />
<br />
Injecting the unmanaged PowerShell-like DLL: <br/>
<img src="https://i.imgur.com/iO1n21j.jpg" height="80%" width="80%" alt="CMD Injection"/>
<br />
<b>To showcase unmanaged PowerShell injection, we can inject an unmanaged PowerShell-like DLL into a random process, such as "spoolsv.exe". We can do this by utilizing the "Invoke-PSInject.ps1" in the manner above.</b>
<br />
<br />
spoolsv.exe Before Injection: <br/>
<img src="https://i.imgur.com/pAITLxd.jpg" height="80%" width="80%" alt="Before Injection"/>
<br />
<br />
spoolsv.exe After Injection: <br/>
<img src="https://i.imgur.com/Hosvhcy.jpg" height="80%" width="80%" alt="After Injection"/>
<br />
<b>After the injection, we observe that "spoolsv.exe" transitions from an unmanaged to a managed state.</b>
<br />
<br />
spoolsv.exe Properties After Injection: <br/>
<img src="https://i.imgur.com/1ifftrK.jpg" height="80%" width="80%" alt="spoolsv.exe Properties After Injection"/>
<br />
<br />
Sysmon Event ID 7 Regarding spoolsv.exe Injection: <br/>
<img src="https://i.imgur.com/6DA0vGM.jpg" height="80%" width="80%" alt="spoolsv.exe Sysmon Event ID 7"/>
<br />
<b>We can refer to both the "Modules" tab of Process Hacker and the Sysmon Event ID 7, we can examine the DLL load information to validate the presence of "clr.dll" or "clrjit.dll".</b>

<h2>Detection Example 3: Detecting Credential Dumping</h2>
Another critical aspect of cybersecurity is detecting credential dumping activities. One widely used tool for credential dumping is <b>Mimikatz</b>. One specific command, <b>"sekurlsa::logonpasswords"</b>, enables the dumping of password hashes or plaintext passwords by accessing the <b>Local Security Authority Subsystem Service (LSASS)</b>. LSASS is responsible for managing user credentials and is a primary target for credential-dumping.
<br />
<br />
We will be executing this attack ourselves to study it:
<br />
<br />

<p align="center">
Mimikatz in Action: <br/>
<img src="https://i.imgur.com/2sKoekn.jpg" height="80%" width="80%" alt="Mimikatz"/>
<br />
<br />
Sysmon Event ID 10: <br/>
<img src="https://i.imgur.com/rVSnlHl.jpg" height="80%" width="80%" alt="Sysmon Event ID 10"/>
<br />
<b>To detect this activity, we will rely on a different Sysmon event. Instead of focusing on DLL loads, we shift our attention to process access events. This event is Sysmon's event ID 10. In the above picture, we observe a random file ("AgentEXE.exe") from a random folder ("Downloads") attempting to access LSASS, it indicates unusual behavior. Additionally, the "SourceUser" being different from the "TargetUser" further emphasizes the abnormaliy.</b>

<h2>Guided Lab Part 2: Event Tracing for Windows (ETW)</h2>
What is ETW? According to Microsoft, Event Tracing for Windows (ETW) is a general-purpse, high-speed tracing facility provided by the Windows operating system. We can use it to bolster our defense capabilities thanks to its high-performance event tracing mechanism.
<br />
<br />
We will utilize ETW to investigate attacks that may evade detection if we rely solely on Sysmon for monitoring and analysis.

<h2>Detection Example 1: Detecting Strange Parent-Child Relationships</h2>
Abnormal parent-child relationships among processes can be indicative of malicious activities. In standard Windows environments, certain processes never call or spawn others. For example, it is highly unlikely to see <b>"calc.exe"</b> spawning <b>"cmd.exe"</b> in a normal Windows environment.
<br />
<br />
By utilizing Process Hacker, we can explore parent-child relationships within Windows.
<br />
<br />

<p align="center">
The Process Hacker Hierarchy: <br/>
<img src="https://i.imgur.com/Exu3WVY.jpg" height="80%" width="80%" alt="Process Hacker Hierarchy"/>
<br />
<b>Sorting the processes by dropdowns in the Processes view reveals a hierarchical representation of the parent-child relationships.</b>
<br />
<br />
