                                                       ** EEDK Lockdown Alert Package**
This repository contains a specialized EEDK (Endpoint Enrollment Deployment Kit) package designed to bypass Windows Session 0 Isolation. It allows administrators to deploy interactive PowerShell GUI notifications to end-users via McAfee/Trellix ePO.

🔴 The Technical Challenge: Session 0 Isolation
When a Trellix Agent client task runs, it executes as NT AUTHORITY\SYSTEM in Session 0, which is a non-interactive background session. If a script triggers a popup natively, it remains invisible to the user, causing the deployment task to hang indefinitely.

🟢 The Solution: ServiceUI Bridge
This package utilizes Microsoft’s ServiceUI.exe to "inject" the PowerShell UI into the active user session (Session 1+) by identifying the explorer.exe process.

🖥️ User Experience
When triggered, the end-user receives a clear, authoritative notification on their desktop:
Header: Technical Support - LOCKDOWN NOTICE
Content: Informs the user that the system is compromised and will be placed in lockdown to protect network integrity.
Logging: All execution details, including hostname and timestamps, are logged to C:\Windows\Temp\McAfeeLogs.

<img width="527" height="321" alt="image" src="https://github.com/user-attachments/assets/863f2557-2ebc-4ca1-b998-3ec7fd190109" />

🛠️ Core Script Logic (RunAlert.cmd)
The primary execution script handles complex architecture redirection to ensure compatibility:
WOW64 Detection: Automatically identifies if a 32-bit Trellix Agent is running on a 64-bit OS.
Path Correction: Uses the sysnative alias to bypass file system redirection and launch the correct 64-bit PowerShell instance.
Safety Exit: If no user is logged in (explorer.exe is missing), the script exits gracefully to prevent background hangs.
Exit Codes: Captures and reports success or failure codes back to the ePO console.

🚀 Deployment Instructions
Verify Files: Ensure ServiceUI.exe, RunAlert.bat, and LockdownAlert.ps1 are in the same directory.
ePO Check-in: Bundle the files into a .zip and check them into the ePO Master Repository.
Test Simulation: To test locally before deployment, run the batch file from a 32-bit Command Prompt (C:\Windows\SysWOW64\cmd.exe) to mirror the Trellix Agent environment.

⚠️ Requirements
OS: Windows 7, 10, or 11 (64-bit recommended).
Agent: Managed Trellix/McAfee Agent.
Permissions: Must be deployed with System privileges (standard for EEDK).

Developed by Raghavendra G C
