# Scenario 3 — Windows Process Creation Monitoring with Sysmon

## Overview

This scenario focuses on monitoring Windows process creation events using **Sysmon (System Monitor)** and collecting this telemetry through the **Wazuh Agent**.

The objective was to gain visibility into process execution on a Windows endpoint, allowing process activity to be monitored and investigated through relevant event information, such as executable paths, command-line arguments, user accounts, and parent processes.

Sysmon was used to generate detailed process creation events, while Wazuh centralized the collected telemetry and applied detection rules for analysis.

## Configuration

### Sysmon Installation

Sysmon was installed on the Windows endpoint using the official Microsoft Sysinternals utility.

The installation was performed through an elevated PowerShell session:

```powershell
.\Sysmon64.exe -accepteula -i
```

After installation, Sysmon began recording system activity in the following Windows Event Channel:

```text
Microsoft-Windows-Sysmon/Operational
```

For this scenario, **Event ID 1 (Process Create)** was particularly relevant because it records process creation activity.

### Wazuh Agent Group Configuration

To centralize the collection configuration, a new agent configuration group named **Sysmon** was created through the Wazuh Dashboard, under **Agent Management → Groups**.

The Windows endpoint was then assigned to this group, allowing the monitoring configuration to be centrally managed and distributed by the Wazuh Manager.

The following configuration was added to the group's shared configuration:

```xml
<agent_config>
  <localfile>
    <location>Microsoft-Windows-Sysmon/Operational</location>
    <log_format>eventchannel</log_format>
  </localfile>
</agent_config>
```

This configuration instructs the Wazuh Agent to collect events from the Sysmon Operational channel using the Windows Event Channel integration.

By assigning the Windows endpoint to the group, the configuration can be managed centrally instead of being maintained individually on the endpoint.

### Sysmon Detection Rules

After configuring event collection, additional Sysmon detection rules were integrated into the Wazuh Manager.

The rules were obtained from the **SOCFortress Wazuh Rules** repository, specifically its Windows Sysmon collection:

[https://github.com/socfortress/Wazuh-Rules/tree/main/Windows_Sysmon](https://github.com/socfortress/Wazuh-Rules/tree/main/Windows_Sysmon)

These rules extend Wazuh's detection capabilities by allowing selected Sysmon events to be identified and classified according to predefined detection logic.

The rules were added to the Wazuh Manager so that incoming Sysmon events could be evaluated against them and generate alerts when their conditions were met.

Using third-party detection rules requires attention to rule ID uniqueness, compatibility, and potential false positives. Detection logic should be reviewed and tested within the target environment before being relied upon.

## Test Execution

Two controlled process execution tests were performed on the Windows endpoint to generate Sysmon telemetry.

**Test 1 — Notepad Execution**

Notepad was launched using PowerShell:

```powershell
start notepad.exe
```

This test was used to generate a basic process creation event and verify whether Sysmon recorded the executable and its execution context.

**Test 2 — PowerShell Execution with Additional Parameters**

A PowerShell process was executed with the following command:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -Command "Get-Date"
```

This test introduced additional command-line parameters to generate a more detailed process execution record.

The `-ExecutionPolicy Bypass` parameter was included to provide an example of a command-line argument that may be relevant during security investigations. Its presence alone does not indicate malicious activity.

## Detection and Analysis

The generated Sysmon events were successfully collected by the Wazuh Agent and processed by the Wazuh Manager.

The investigation focused on **Event ID 1 (Process Create)** and the information provided by its associated event fields.

| Event Field | Description |
|---|---|
| `win.system.eventID` | Identifies the Sysmon event type. Value `1` represents process creation. |
| `win.eventdata.image` | Full path of the executable responsible for the event. |
| `win.eventdata.commandLine` | Command-line arguments used to execute the process. |
| `win.eventdata.user` | User account under which the process was executed. |
| `win.eventdata.processId` | Process identifier (PID) assigned to the newly created process. |
| `win.eventdata.parentImage` | Full path of the parent process that initiated the execution. |
| `win.eventdata.parentCommandLine` | Command-line arguments associated with the parent process. |
| `win.eventdata.parentProcessId` | Unique identifier assigned by Sysmon to the process. |

These fields provide useful context for investigating process execution, including identifying unusual command-line parameters, tracing parent-child process relationships, and examining executable metadata.

An important distinction during the analysis is that **event collection and alert generation are not the same process**. Sysmon can successfully generate an event and Wazuh can collect it without necessarily producing a security alert. Alert generation depends on the applicable Wazuh rules and their configured severity levels.

## Evidence

The following evidence documents the configuration and validation of the monitoring process:

**1. Wazuh Agent Configuration Group**

<p align="left">
  <img src="third_scenario_prints/sysmon group.png" width="60%">
  <img src="third_scenario_prints/group config.png" width="60%">
</p>

**2. Sysmon Detection Rules**

<p align="left">
  <img src="third_scenario_prints/rules1.png" width="60%">
  <img src="third_scenario_prints/rules2.png" width="60%">
</p>


**3. Sysmon Event Viewer**

<p align="left">
  <img src="third_scenario_images/notepad sysmon.png" width="60%">
  <img src="third_scenario_images/powershell sysmon.png" width="60%">
</p>

**4. Wazuh Event Collection and Analysis**

<p align="left">
  <img src="third_scenario_images/notepad wazuh 1.png" width="60%">
  <img src="third_scenario_images/notepad wazuh 2.png" width="60%">
  <img src="third_scenario_images/notepad wazuh 3.png" width="60%">
  <img src="third_scenario_images/powershell wazuh 1.png" width="60%">
  <img src="third_scenario_images/powershell wazuh 2.png" width="60%">
  <img src="third_scenario_images/powershell wazuh 3.png" width="60%">
</p>

## Findings

The tests confirmed that Sysmon successfully recorded process creation activity on the Windows endpoint and that the Wazuh Agent collected events from the Sysmon Operational channel.

The centralized agent group configuration allowed the Sysmon event collection settings to be managed through the Wazuh Dashboard, while the integrated detection rules extended the environment's ability to identify and classify selected Sysmon activity.

The collected telemetry provided visibility into executable paths, command-line arguments, user context, and parent processes, allowing individual process executions to be examined through Wazuh.

The tests also demonstrated the importance of examining event metadata rather than relying exclusively on alert descriptions. Identifying the event provider, channel, and event ID is essential to distinguish Sysmon telemetry from other Windows event sources.

## Security Relevance

Process creation monitoring is an important component of endpoint security because it provides visibility into how applications and commands are executed within a system.

The collected information can support investigations involving:

- **Suspicious command execution:** Identifying unusual or potentially risky command-line arguments.
- **Process relationship analysis:** Examining parent-child relationships to understand how a process was initiated.
- **Execution of unknown binaries:** Investigating executable paths and associated file hashes.
- **Living-off-the-land techniques:** Identifying potentially suspicious use of legitimate Windows utilities and administrative tools.

These capabilities are particularly useful when investigating techniques associated with the MITRE ATT&CK framework, including Command and Scripting Interpreter (T1059) and System Binary Proxy Execution (T1218).

However, individual process creation events do not establish malicious activity on their own. Effective detection requires examining the execution context, surrounding events, and additional indicators.

## Conclusion

This scenario successfully demonstrated the integration of Sysmon with Wazuh for Windows process creation monitoring.

By combining centralized agent configuration, Sysmon event collection, and additional detection rules, the environment provided visibility into process execution details that can support endpoint investigations and security monitoring activities.

The implementation established a foundation for more advanced detection capabilities, including custom Wazuh rules, suspicious command-line detection, and correlation of process activity with other security events.