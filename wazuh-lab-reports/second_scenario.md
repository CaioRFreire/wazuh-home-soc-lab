# Scenario 02 — File Integrity Monitoring (FIM)

## Overview

This scenario demonstrates how Wazuh's File Integrity Monitoring (FIM) can monitor changes within a specific directory on a Windows endpoint.

The test focuses on detecting file creation and deletion events, allowing changes to be tracked and investigated through the Wazuh Dashboard.

**Environment:**
- **Endpoint:** Windows
- **Monitoring Platform:** Wazuh
- **Module:** File Integrity Monitoring (Syscheck)
- **Monitoring Mode:** Real-time
- **Monitored Directory:** `C:\Users\caior\Downloads\fim-test`

## FIM Configuration

The following rule was added to the Windows agent's `ossec.conf` file to enable real-time monitoring of the selected directory.

```xml
<directories realtime="yes">C:\Users\caior\Downloads\fim-test</directories>
```

## Test Execution

Two file operations were performed within the monitored directory:

1. **File Creation:** A new `test.txt` file was created.
2. **File Deletion:** The same file was deleted after its creation.

**Expected behavior:**
- Wazuh detects the file creation.
- Wazuh detects the file deletion.
- Both events are collected and made available in the Wazuh Dashboard for analysis.

## Detection and Analysis

The generated FIM events were examined in the Wazuh Dashboard to verify that the file operations were correctly identified.

The following fields were considered during the analysis:

| Field | Purpose |
|---|---|
| `syscheck.event` | Identifies the type of file operation, such as `added` or `deleted`. |
| `syscheck.path` | Identifies the affected file and its location. |
| `syscheck.md5_after` | Provides the file's MD5 hash after creation or modification, when available. |
| `syscheck.sha256_after` | Provides the file's SHA-256 hash after creation or modification, when available. |
| `syscheck.size_after` | Indicates the file size after creation or modification, when available. |
| `timestamp` | Indicates when the event was generated. |

## Evidence

**1. FIM Configuration**

<p align="left">
  <img src="second_scenario_images/ossec conf file.png" width="70%">
</p>

**2. File Creation and Deletion Events**

<p align="left">
  <img src="second_scenario_images/wazuh fim dashboard.png" width="70%">
</p>

**3. File Creation Event Details**

<p align="left">
  <img src="second_scenario_images/file added event.png" width="70%">
</p>

**4. File Deletion Event Details**

<p align="left">
  <img src="second_scenario_images/file deleted event.png" width="70%">
</p>

## Findings

- **File Creation Detection:** 
Wazuh successfully detected the creation of the test.txt file within the monitored directory, generating an added event.

- **File Deletion Detection:**
The subsequent deletion of the same file was also detected, generating a deleted event.

- **Event Information:**
The collected events provided information about the affected file, including its path and available metadata, allowing both operations to be identified and investigated through the Wazuh Dashboard.

- **Monitoring Validation:**
Real-time monitoring worked as expected, successfully identifying both file operations without requiring a manual scan.

## Security Relevance

File Integrity Monitoring helps detect unauthorized or unexpected changes to files and directories.

Monitoring file creation and deletion can support the identification of suspicious activity, such as unauthorized file placement, malware deployment, and attempts to remove or tamper with files.

Although these events do not necessarily indicate malicious activity, they provide valuable visibility into changes that may require further investigation.

## Conclusion

The test was successfully completed, confirming that Wazuh's File Integrity Monitoring can detect file creation and deletion in a specific Windows directory.

This capability can help detect suspicious activities such as unauthorized file placement, malware deployment, and attempts to remove or tamper with files.
