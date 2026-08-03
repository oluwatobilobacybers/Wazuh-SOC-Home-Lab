# File Integrity Monitoring (FIM)

## Objective

Demonstrate Wazuh's File Integrity Monitoring (FIM) capability by detecting the creation and modification of files on a monitored Linux endpoint.

---

# Overview

File Integrity Monitoring is one of Wazuh's core security features. It continuously monitors configured directories and files for changes, generating alerts whenever files are created, modified, deleted, or have their permissions changed.

This capability helps security teams detect:

- Unauthorized file changes
- Malware activity
- Web shell deployment
- Configuration tampering
- Insider threats

---

# Environment

| Component | Details |
|----------|----------|
| Wazuh Manager | Ubuntu Server 24.04 |
| Wazuh Agent | Ubuntu Server |
| Analyst Workstation | Kali Linux |
| Dashboard | Wazuh Dashboard |

---

# Configuration

The following directories were monitored by Wazuh Syscheck:

```xml
<directories>/etc,/usr/bin,/usr/sbin</directories>
<directories>/bin,/sbin,/boot</directories>
```

Verification command:

```bash
grep "<directories" /var/ossec/etc/ossec.conf
```

---

# Test Procedure

A test file was created inside the monitored `/etc` directory.

```bash
sudo touch /etc/wazuh_test_file
```

The file was then modified.

```bash
echo "Testing Wazuh FIM" | sudo tee /etc/wazuh_test_file
```

---

# Detection

Within a short period, Wazuh generated File Integrity Monitoring alerts.

The dashboard reported events indicating:

- File Added
- File Modified

These events appeared under:

- Security Events
- File Integrity Monitoring (FIM)

---

# Investigation

The generated alerts contained:

- Event time
- Endpoint name
- File path
- Event type
- Rule description
- Severity level

The analyst confirmed that the activity matched the expected test actions.

---

# Result

The File Integrity Monitoring module successfully detected file creation and modification inside a monitored directory.

This confirms that:

- Wazuh Agent was functioning correctly.
- Syscheck was properly configured.
- Alert generation was operational.
- The dashboard successfully received and displayed security events.

---

# Skills Demonstrated

- File Integrity Monitoring
- Linux Administration
- Endpoint Monitoring
- Security Event Analysis
- Incident Investigation
- Wazuh SIEM
