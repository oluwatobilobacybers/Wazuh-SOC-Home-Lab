# File Integrity Monitoring (FIM)

## Overview

File Integrity Monitoring (FIM) is a security control used to detect unauthorized or unexpected changes to files and directories.

In this lab, Wazuh FIM was configured on the Ubuntu Server endpoint to monitor a dedicated test directory:

`/tmp/wazuh-test/`

The objective was to validate that Wazuh could detect:

- File creation
- File modification
- File deletion

The resulting events were collected by the Wazuh Agent and analyzed by the Wazuh Manager.


## Lab Environment

| Component | Details |
|---|---|
| Wazuh Manager | Kali Linux |
| Manager IP | 172.20.10.4 |
| Monitored Endpoint | Ubuntu Server 24.04 LTS |
| Agent IP | 172.20.10.5 |
| Wazuh Agent | ubuntu-agent |
| Monitoring Method | FIM / Syscheck |
| Test Directory | `/tmp/wazuh-test/` |



# FIM Configuration

The Ubuntu endpoint was configured to monitor the test directory using Wazuh File Integrity Monitoring.

The monitoring mode used during testing was:

`realtime`

This allowed Wazuh to detect file-system changes shortly after they occurred.

The monitored directory was:


/tmp/wazuh-test/


# FIM Validation Methodology

The FIM implementation was validated using controlled file-system activities.

The following sequence was used:

Create file
     ↓
Modify file
     ↓
Investigate alert
     ↓
Delete file
     ↓
Investigate alert

This allowed each type of FIM event to be independently validated.

# Test 1 — File Creation

A test file was created inside the monitored directory.

Example:
```bash
sudo touch /tmp/wazuh-test/active-response-test
```

Wazuh generated a File Integrity Monitoring alert indicating that a new file had been added.

The relevant Wazuh rule was:
Rule ID: 554
Description: File added to the system.
Level: 5

The event showed:
File '/tmp/wazuh-test/active-response-test' added
Mode: realtime

This confirmed that Wazuh successfully detected file creation.

# Test 2 — File Modification

A second test file was modified after its initial creation.

Wazuh generated the following alert:
Rule ID: 550
Description: Integrity checksum changed.
Level: 7

The event indicated that the following attributes changed:
size
mtime
md5
sha1
sha256

The alert recorded both the previous and new cryptographic hashes.

Example:
Old md5sum:
28ac8f1ba8006193fa5969c636a92ea4

New md5sum:
9554b8ab6a8adf7c307933029422d9bc

The SHA-1 and SHA-256 values were also changed.

This demonstrates that Wazuh was not simply detecting that the file existed; it was detecting changes to the file's integrity characteristics.

# Test 3 — File Deletion

The modified test file was subsequently deleted.

Wazuh generated:
Rule ID: 553
Description: File deleted.
Level: 7

The event contained:
File '/tmp/wazuh-test/second-test.txt' deleted
Mode: realtime

This confirmed that Wazuh successfully detected file deletion.

# FIM Alert Summary

| Activity          | Rule ID | Alert                      | Level | Result     |
| ----------------- | ------: | -------------------------- | ----: | ---------- |
| File creation     |     554 | File added to the system   |     5 | ✅ Detected |
| File modification |     550 | Integrity checksum changed |     7 | ✅ Detected |
| File deletion     |     553 | File deleted               |     7 | ✅ Detected |

# Alert Investigation

The generated alerts were investigated directly from:
/var/ossec/logs/alerts/alerts.json

Example investigation command:
sudo grep -i "second-test.txt" /var/ossec/logs/alerts/alerts.json | tail -5

The investigation confirmed that the events contained:

- Event timestamp
- Rule ID
- Rule description
- Alert severity
- Agent information
- File path
- File size
- File ownership
- Modification time
- MD5 hash
- SHA-1 hash
- SHA-256 hash
- Event type

This information provides the basic evidence required for a SOC analyst to investigate file-integrity events.


# Example Alert Evidence

For the file modification event, Wazuh reported:

File '/tmp/wazuh-test/second-test.txt' modified

Changed attributes:
size,mtime,md5,sha1,sha256

Size changed from '22' to '26'

The event also contained the previous and new cryptographic hashes.

This provides useful evidence when determining whether a file was unexpectedly altered.


# MITRE ATT&CK Mapping

The modification event was mapped by Wazuh to:
T1565.001
Stored Data Manipulation

T1565.001 falls under the:
Impact

tactic.

The mapping demonstrates how a seemingly simple file modification can be contextualized within a broader threat-detection framework.


# SOC Analyst Perspective

From a SOC analyst perspective, a FIM alert should not automatically be considered malicious.

The analyst should determine:

1. What file was changed?
2. Which endpoint was affected?
3. When did the change occur?
4. Which user or process performed the change?
5. Was the change expected?
6. What attributes changed?
7. Did the file contents or hashes change?
8. Was there a related authentication or privilege-escalation event?
9. Does the activity correspond with an authorized administrative action?
10. Does the event require escalation or response?

For example, the modification observed in this lab was intentional because it was generated as part of a controlled security test.

In a production environment, an unexpected modification to a sensitive system file could require further investigation.


# Related Event Correlation

During the investigation, Wazuh also recorded sudo activity associated with the test operations.

For example:
Successful sudo to ROOT executed.

The corresponding command was recorded as:
/usr/bin/tee /tmp/wazuh-test/second-test.txt

This demonstrates an important SOC investigation principle:
A security alert should be investigated together with related events rather than in isolation.

The FIM alert identifies the file change, while the sudo event provides additional context about the command that performed the change.


# Validation Result

The FIM implementation was successfully validated.

The Wazuh environment detected all three primary file-system activities tested:

Creation       → Detected
Modification   → Detected
Deletion       → Detected

Therefore:

FIM Configuration
        ↓
Realtime Monitoring
        ↓
File-System Event
        ↓
Wazuh Agent
        ↓
Wazuh Manager
        ↓
Rule Matching
        ↓
Alert Generation
        ↓
SOC Investigation


# Lessons Learned

The FIM exercise demonstrated several important concepts:

- FIM provides visibility into file-system changes.
- Realtime monitoring can detect changes shortly after they occur.
- File modification alerts can include cryptographic hash changes.
- Alert severity varies according to the detected event.
- File events should be correlated with other system events.
- A detected change does not automatically mean malicious activity.
- Investigation requires understanding the context surrounding the alert.
- Wazuh provides useful evidence for SOC investigation.
- Security controls must be validated through controlled testing.


# Evidence

The following screenshots document the FIM implementation and alert investigation performed during the lab.

## Wazuh Dashboard — FIM Alert

![Wazuh Dashboard](screenshots/wazuh-dashboard-fim-alert.png)

The Wazuh Dashboard was used to review the generated File Integrity Monitoring alerts.

---

## FIM File Creation, Modification and Deletion

![FIM File Creation Modification Deletion](screenshots/fim-file-added-modified-deleted-alert.png)

This evidence demonstrates the detection of file-system changes within the monitored directory.

---

## FIM Modification Alert

![FIM Modification Alert](screenshots/fim-rules-modified-alert.png)

This alert demonstrates the detection of file integrity changes, including changes to file size, modification time, and cryptographic hashes.

---

## Wazuh Agent FIM Alert

![Wazuh Agent FIM Alert](screenshots/wazuh-agent-fim-alert.png)

This screenshot provides evidence of the FIM event being associated with the monitored Wazuh agent.
