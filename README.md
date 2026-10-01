# Wazuh SOC Monitoring Lab

## Overview
This project demonstrates a hands-on Security Operations Center (SOC) monitoring lab using Wazuh.

The lab is designed to monitor a Windows endpoint, collect security logs, detect suspicious activity, and investigate security events in a controlled environment.

## Objectives
- Deploy Wazuh on Ubuntu Server
- Connect a Windows endpoint using Wazuh Agent
- Monitor Windows security events
- Detect failed login attempts
- Monitor file integrity changes
- Analyze security alerts
- Document findings with screenshots

## Lab Environment
- Oracle VirtualBox
- Ubuntu Server
- Windows 10/11
- Wazuh SIEM/XDR
- Local isolated testing environment

## Project Architecture

Windows Endpoint  
↓  
Wazuh Agent  
↓  
Wazuh Server  
↓  
Wazuh Dashboard  
↓  
Security Alerts & Investigation

## Project Status

🚧 In Progress

## Disclaimer

This project is created strictly for educational purposes in an authorized local lab environment.

## Failed Login Detection

Wazuh successfully detected three failed Windows login attempts from the monitored endpoint `Windows-Lab-01`.

The events were identified as authentication failures caused by an unknown user or incorrect password.

![Failed Login Detection](screenshots/05-failed-login-detection.png)

## File Integrity Monitoring

Wazuh File Integrity Monitoring successfully detected a change made to the monitored file inside `C:\Wazuh-Test`.

The detected event was classified as an integrity checksum change.

![File Integrity Monitoring](screenshots/06-file-integrity-monitoring.png)

## File Creation Detection

Wazuh successfully detected the creation of a new file inside the monitored directory `C:\Wazuh-Test`.

The event was identified as:

- Rule: `File added to the system`
- Rule ID: `554`
- Agent: `Windows-Lab-01`

![File Creation Detection](screenshots/07-file-created-detection.png)
