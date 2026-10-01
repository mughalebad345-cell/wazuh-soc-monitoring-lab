# Wazuh SOC Monitoring Lab Documentation

## Project Overview
This project demonstrates a small Security Operations Center (SOC) monitoring lab using Wazuh.

The lab was built to monitor a Windows endpoint, collect security events, detect suspicious activity, and investigate alerts from a centralized Wazuh dashboard.

## Lab Environment
- Oracle VirtualBox
- Ubuntu Server
- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- Windows 10 Pro
- Wazuh Windows Agent
- Windows Defender
- PowerShell
- GitHub for documentation and evidence

## Architecture

Windows 10 Endpoint
    |
    | Wazuh Agent
    v
Ubuntu Wazuh Server
    |
    | Wazuh Manager
    | Wazuh Indexer
    | Wazuh Dashboard
    v
Security Alerts and Investigation

## Network Setup
The Ubuntu Wazuh server was initially running in NAT mode.

To allow the Windows host to communicate directly with the Wazuh server, the VirtualBox network adapter was changed to Bridged Adapter mode.

Example lab addresses used during testing:
- Wazuh Server: 192.168.100.106
- Windows Endpoint: 192.168.100.66

These addresses are lab-specific and may be different in another environment.

## Wazuh Server Deployment
Wazuh was installed on Ubuntu Server as an all-in-one deployment.

The deployment included:
- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard

The dashboard was accessed from the Windows host using the Wazuh server IP address over HTTPS.

## Windows Agent Deployment
The Wazuh Windows agent was installed on the monitored Windows endpoint.

Agent name:
- Windows-Lab-01

The agent service was verified using PowerShell:

```powershell
Get-Service wazuhsvc
```

The service status was confirmed as Running and the endpoint appeared as Active in the Wazuh dashboard.

## Detection Test 1: Failed Login Detection

### Objective
Detect failed Windows authentication attempts.

### Test
Multiple incorrect passwords were entered at the Windows login screen.

### Result
Wazuh detected the failed authentication attempts.

Observed information:
- Windows Event ID: 4625
- Wazuh Rule: Logon Failure - Unknown user or bad password
- Wazuh Rule ID: 60122

### Flow
Wrong Password
-> Windows Security Event
-> Wazuh Agent
-> Wazuh Manager
-> Threat Hunting Alert

## Detection Test 2: File Integrity Monitoring

### Objective
Monitor a test directory for file changes.

### Monitored Directory
```text
C:\Wazuh-Test
```

The following configuration was added to the Wazuh agent:

```xml
<directories realtime="yes">C:\Wazuh-Test</directories>
```

The Wazuh agent service was restarted after the configuration change.

### File Modification Result
A monitored file was modified.

Wazuh detected:
- Rule: Integrity checksum changed
- Rule ID: 550

### File Creation Result
A new file was created in the monitored directory.

Wazuh detected:
- Rule: File added to the system
- Rule ID: 554

### File Deletion Result
The created file was deleted.

Wazuh detected:
- Rule: File deleted
- Rule ID: 553

## Detection Test 3: Process Creation Monitoring

### Objective
Monitor process creation on the Windows endpoint.

Process creation auditing was enabled using:

```powershell
auditpol /set /subcategory:"Process Creation" /success:enable
```

Test processes included:
- whoami
- ipconfig
- notepad.exe

### Result
Wazuh detected process creation events.

Observed information:
- Windows Event ID: 4688
- Wazuh Rule: A process was created
- Wazuh Rule ID: 67027

### Process Investigation
A specific Notepad process was investigated.

Observed process relationship:
- New Process: C:\Windows\System32\notepad.exe
- Parent Process: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe

This demonstrated how Wazuh can help investigate parent-child process relationships.

## Detection Test 4: Windows Defender Integration

### Objective
Collect Windows Defender Operational events in Wazuh.

The following event channel was added to the Wazuh agent configuration:

```xml
<localfile>
  <location>Microsoft-Windows-Windows Defender/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

The Wazuh agent service was restarted after the change.

### Safe Test
A standard EICAR antivirus test string was used.

The EICAR test is not real malware. It is a harmless antivirus test pattern used to verify security product detection.

### Result
Windows Defender detected the test file and generated a Defender event.

Wazuh collected and displayed the detection.

Observed information:
- Windows Defender Event ID: 1116
- Wazuh Rule ID: 62123
- Wazuh Rule Level: 12
- Agent: Windows-Lab-01

### Flow
EICAR Test File
-> Windows Defender Detection
-> Defender Operational Event
-> Wazuh Agent
-> Wazuh Manager
-> Wazuh Dashboard Alert

## PowerShell Role in the Lab
PowerShell was used to configure and test the Windows endpoint.

Examples:
- Start and restart the Wazuh agent service
- Verify the Wazuh service status
- Create and modify test files
- Delete test files
- Enable process creation auditing
- Verify Windows Defender status
- Generate controlled test activity

PowerShell generated or configured activity. Wazuh monitored and analyzed the resulting events.

## Project Results
The lab successfully demonstrated:
- Wazuh server deployment
- Windows endpoint onboarding
- Failed login detection
- File modification detection
- File creation detection
- File deletion detection
- Process creation monitoring
- Parent-child process investigation
- Windows Defender event integration
- Centralized alert investigation

## What I Learned
This project helped demonstrate how a SIEM/XDR platform can:
- Collect endpoint security logs
- Centralize security monitoring
- Detect authentication failures
- Detect file tampering
- Monitor process execution
- Investigate process relationships
- Integrate endpoint antivirus events
- Support SOC-style alert investigation

## Security Note
This lab was created only for educational purposes in an authorized local testing environment.

No real malware was used. The antivirus test used the safe EICAR standard test string.

## Sensitive Data Notice
Passwords, private credentials, authentication keys, and other sensitive values should never be committed to a public GitHub repository.
