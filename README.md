                                                **EEDK-Lockdown-Alert**

EEDK Lockdown Alert Package
A specialized McAfee/Trellix EEDK (Endpoint Enrollment Deployment Kit) package designed to bypass Session 0 Isolation and display interactive PowerShell GUI notifications to logged-in users.

🔴 The Problem: Session 0 Isolation
When a Trellix Agent client task runs, it executes as NT AUTHORITY\SYSTEM in Session 0.

The Conflict: Session 0 is non-interactive. 
If your script triggers a popup, it stays hidden in the background, causing the task to hang indefinitely as it waits for a user response that no one can see.
The Architecture Gap: The Trellix Agent is a 32-bit application. On 64-bit Windows, it runs in WOW64 mode, which redirects file paths and can prevent the execution of 64-bit PowerShell tools.

🟢 The Solution: ServiceUI Bridge
This package utilizes Microsoft’s ServiceUI.exe (from the Microsoft Deployment Toolkit) to bridge the gap between the System Session (0) and the User Session (1+). It detects the active explorer.exe process and "injects" the PowerShell UI into the user's visible desktop.

🛠 Features
Session Bridging: Automatically moves the UI from SYSTEM to the active user's desktop.
Architecture Awareness: Handles sysnative path redirection to ensure 64-bit PowerShell runs correctly from a 32-bit Agent.
Safety Checks: Includes a PROCESSOR_ARCHITECTURE check to prevent crashes on legacy 32-bit operating systems.
Centralized Logging: Detailed audit logs are created at C:\Windows\Temp\McAfeeLogs.

📂 Package Structure
LaunchLockdown.cmd: The main entry point (the batch file called by EEDK).
LockdownAlert.ps1: The PowerShell script containing your custom GUI/Message logic.
ServiceUI.exe: The Microsoft utility used for session switching.
EEDK Manifest Files: Required files for ePO integration.

🚀 Deployment Instructions
1. Preparation
Ensure your LockdownAlert.ps1 contains the specific message you wish to show the end user.

2. ePO Integration
Check the .zip package into your ePO Master Repository.

----------------------------------------------------------------------------------------------------------------------------------------
**Customizing the Popup Message**
If you need to modify the popup notification content, follow these steps:
Extract the provided ZIP package.
Open the file RunAlert.cmd using a text editor such as Notepad++.
Update the popup message or any required parameters as needed.
Save the changes.
Recreate the EEDK package using the modified .cmd file.

Important:
Ensure that ServiceUI.exe remains in the same folder as RunAlert.cmd when creating the new EEDK package. The script relies on ServiceUI.exe to launch the popup in the active user session.
----------------------------------------------------------------------------------------------------------------------------------------


Create a Product Deployment Task.
Target your desired System Tree groups or specific tags.
Schedule the task to "Run Immediately" or at your preferred interval.

3. Verification & Logs
If a deployment shows as "Completed" in ePO but the user didn't see a popup, check the local logs on the endpoint:
             C:\Windows\Temp\McAfeeLogs\%COMPUTERNAME%_Lockdown_[Date].txt

🧪 Testing (The "SysWOW64" Method)
To accurately simulate how the Trellix Agent will run this script, do not simply double-click the .cmd file. You must simulate the 32-bit environment:
Open a 32-bit Command Prompt:
Win + R -> Paste: C:\Windows\SysWOW64\cmd.exe
Navigate to your script folder.
Run LaunchLockdown.cmd.

Check the logs for: INFO: 64-bit OS (WOW64) detected.

⚠️ Requirements
Operating System: Windows 7, 10, 11 (64-bit recommended).

Management: Trellix Agent managed via ePO.

Dependencies: PowerShell 5.1+.
            
             **Created by Raghavendra G C**
