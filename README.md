# Wazuh Home SOC Lab

A hands-on cybersecurity project focused on endpoint monitoring, security event collection, and detection using **Wazuh** and **Sysmon**.

The lab was built in a virtualized environment to simulate basic security monitoring activities on a Windows endpoint.

## Technologies Used

- **Wazuh** — Security event collection, monitoring, and analysis.
- **Sysmon** — Detailed Windows system activity monitoring.
- **Windows 11** — Monitored endpoint.
- **Ubuntu Server** — Wazuh Manager and Dashboard.
- **VirtualBox** — Virtualization environment.

## Lab Architecture

The environment consists of a Wazuh server running on Ubuntu Server and a Windows endpoint connected through a Wazuh Agent.

Sysmon generates detailed endpoint telemetry, which is collected by the Wazuh Agent and forwarded to the Wazuh Manager for analysis.

## Implemented Scenarios

### 1. Windows Failed Logon Detection

- Generated failed and successful Windows authentication attempts.
- Monitored Windows Security Events (4625 and 4624).
- Analyzed authentication-related event fields using Wazuh.

[View Scenario 1](/wazuh-lab-reports/first_scenario.md)

### 2. File Integrity Monitoring (FIM)

- Configured real-time monitoring of a Windows directory.
- Tested file creation and deletion.
- Analyzed file integrity events and associated metadata.

[View Scenario 2](/wazuh-lab-reports/second_scenario.md)

### 3. Windows Process Creation Monitoring with Sysmon

- Installed and configured Sysmon on the Windows endpoint.
- Integrated Sysmon event collection through Wazuh Agent groups.
- Integrated additional detection rules from SOCFortress.
- Monitored and analyzed process creation events (Event ID 1).

[View Scenario 3](/wazuh-lab-reports/third_scenario.md)

## Key Skills Demonstrated

- SIEM Configuration and Monitoring
- Windows Event Log Analysis
- Endpoint Monitoring
- Sysmon Integration
- File Integrity Monitoring
- Security Event Investigation
- Detection Rules Integration
- Linux Administration
- Virtualized Lab Deployment

## Project Objective

The objective of this project was to gain practical experience with SIEM deployment, endpoint telemetry collection, and security event analysis while developing a foundation in SOC operations and defensive security.

This project was developed in a controlled home lab environment for educational and portfolio purposes. Detection results are limited to the scenarios and configurations documented in this repository.
