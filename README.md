# ThreatX-Guardian
Step 1:
Plan the Project Objectives
The goal of this project is to:

Detect suspicious activities (like failed logins or unauthorized file access).
Log security events for analysis.
Generate alerts via email.
Optionally, disable suspicious user accounts to protect the system.

Step 2:
Setup Your Environment
Ensure PowerShell Compatibility:
Open PowerShell and check the version:

powershell
cmd: $PSVersionTable.PSVersion
You need PowerShell 5.1 or later.

Enable Script Execution:
Allow your system to run custom PowerShell scripts by running:

powershell
cmd: Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
Choose Yes when prompted.

Step 3:
Create Necessary Folders
Create a Folder for Logs:
Open PowerShell and run:

powershell
cmd: New-Item -ItemType Directory -Path "C:\SecurityLogs"
Create a Folder for the Script:
Create a working directory to store the script:

powershell
cmd:New-Item -ItemType Directory -Path "C:\SecurityScripts"

Step 4: 
Write the PowerShell Script
Open Notepad and copy the script below:

powershell
# SMTP Configuration
$smtpServer = "smtp.gmail.com"  # Change for your email provider
$fromEmail = "your-email@gmail.com"  # Your email
$toEmail = "admin-email@gmail.com"  # Where alerts are sent
$secureCredential = Get-Credential  # Enter your email credentials securely

# Log File Configuration
$logFile = "C:\SecurityLogs\IncidentReport.log"
Write-Host "Monitoring System for Security Events..."

# Event Log Monitoring: Check for failed logins (Event ID 4625)
$events = Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625} -MaxEvents 5

if ($events) {
    # Log each event and send email alerts
    foreach ($event in $events) {
        $logEntry = "Suspicious Login Detected: $($event.Message)"
        Add-Content -Path $logFile -Value $logEntry

        # Send Email Alert
        $subject = "Security Alert: Failed Login Attempt"
        $body = $logEntry
        Send-MailMessage -SmtpServer $smtpServer -Credential $secureCredential `
            -From $fromEmail -To $toEmail -Subject $subject -Body $body
    }
}

# Optional: Disable Suspicious User
$suspiciousUser = "targetUser"  # Replace with the username to disable
if ($events.Count -ge 3) {
    Write-Host "Critical: Multiple failed logins detected for user $suspiciousUser!"
    Disable-LocalUser -Name $suspiciousUser
    Write-Host "User $suspiciousUser disabled for security."
}

Write-Host "Incident Response Monitoring Complete."
Step 5: Save the Script
Save the file as IncidentResponse.ps1 inside C:\SecurityScripts.
Select All Files in the Save As dialog box to ensure it saves as a script, not a text file.
Step 6: Test the Script Execution
Open PowerShell as Administrator:

Search for "PowerShell," right-click, and select Run as Administrator.
Navigate to Script Directory:

powershell
cmd: cd C:\SecurityScripts
Run the Script:

powershell
cmd: .\IncidentResponse.ps1

Step 7:
Verify Results
Check Log Files:
Look for log entries in the C:\SecurityLogs\IncidentReport.log file.

Check Email Alerts:
Verify if security alerts have been sent to the specified email.

Verify User Disable:
Check if the specified user (targetUser) is disabled.

Step 8: 
Optional Improvements
Add More Event Filters: Monitor additional events like file access or privilege escalation.
Enhance Security: Encrypt the SMTP connection and use secure credential storage.
Better Reporting: Create PowerBI dashboards for detailed threat insights.
