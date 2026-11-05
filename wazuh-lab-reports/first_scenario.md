# Scenario 01 — Windows Failed Logon Detection

## Overview

This scenario tests Wazuh's ability to monitor and collect failed authentication events from a Windows endpoint.

The test focuses on Windows Security Event ID `4625`, which is generated when an account fails to log on.

**Environment:**
- **Endpoint:** Windows
- **Monitoring Platform:** Wazuh
- **Log Source:** Windows Security Event Log
- **Event ID:** `4625`

## Test Execution

Four consecutive failed login attempts will be performed on the Windows endpoint, followed by one successful login using the correct credentials.

**Expected behavior:**
- Windows generates an Event ID `4625` for each failed attempt.
- Windows generates an Event ID `4624` for the successful login.
- Wazuh collects these events from the endpoint.
- The events become available in the Wazuh Dashboard.

## Detection and Analysis

The following fields were examined to understand the authentication failures:

| Field | Purpose |
|---|---|
| `AccountName` | Identifies the account involved in the attempt. |
| `LogonType` | Identifies the type of logon attempted. |
| `IpAddress` | Indicates the source IP address. |
| `Failure Reason` | Provides details about the reason for the authentication failure. |
| `AccountDomain` | Identifies the domain or local computer associated with the account. |

### Evidence

**Windows Event Viewer**

<p align="left">
  <img src="first_scenario_images/Event Viewer 1.png" width="70%">
  <img src="first_scenario_images/Event Viewer 2.png" width="70%">
</p>

**Wazuh Dashboard**

<p align="left">
  <img src="first_scenario_images/Wazuh 1.png" width="70%">
  <img src="first_scenario_images/Wazuh 2.png" width="70%">
</p>

## Findings

- **Event Detection:** 
Wazuh successfully collected the Windows Security events, including failed logon attempts (Event ID 4625) and the subsequent successful login (Event ID 4624).

- **Authentication Pattern:**
The test demonstrated that multiple failed authentication attempts can be observed alongside a subsequent successful login, providing visibility into the authentication sequence.

- **Event Information:** 
The collected events provided relevant information about the authentication attempts, including the target account username, account domain, logon type, failure details and account ip.

- **Investigation Potential:** 
Monitoring these events can help identify unusual authentication patterns and support investigations into potential brute-force attacks or unauthorized access attempts.

## Security Relevance

Failed authentication events can help identify suspicious activity, such as brute-force attacks and password spraying.

Although a single failed login does not necessarily indicate malicious activity, monitoring repeated failures can help an analyst identify unusual authentication patterns and investigate potential unauthorized access attempts.

## Conclusion

The test was successfully completed, confirming that Wazuh can collect and monitor Windows authentication events. Both failed and successful login attempts were correctly identified, allowing the authentication sequence to be analyzed through the Wazuh Dashboard.

This scenario demonstrated how centralized event monitoring can provide visibility into authentication activity and support the identification of suspicious login patterns, making it a useful capability for security monitoring and incident investigation.
