# Overview

This exercise demonstrates a complete incident-response workflow using Wazuh SIEM to detect, investigate, and automatically respond to suspicious SSH authentication activity.

The exercise simulated repeated SSH authentication attempts against the monitored Ubuntu endpoint and validated how Wazuh:

- Detected authentication attempts involving a nonexistent user.
- Identified repeated authentication failures.
- Classified the activity as brute-force/password-guessing behaviour.
- Triggered an automated firewall response.
- Recorded the response in the Wazuh alert and active-response logs.
- Captured subsequent legitimate SSH authentication events.
- Provided centralized evidence for incident investigation.

The exercise was performed within the isolated Wazuh SOC home laboratory environment.

The Incident-Response workflow demonstrated was:

Detect → Analyze → Correlate → Contain → Validate → Recover → Document

# Lab Environment

| Component                   | Details                                   |
| --------------------------- | ----------------------------------------- |
| Wazuh Manager               | Kali Linux                                |
| Wazuh Manager IP            | `172.20.10.4`                             |
| Monitored Endpoint          | Ubuntu Server 24.04.4 LTS                 |
| Ubuntu Agent IP             | `172.20.10.5`                             |
| Wazuh Version               | `4.14.7`                                  |
| Service Under Investigation | OpenSSH                                   |
| Destination Port            | TCP/22                                    |
| SIEM                        | Wazuh                                     |
| Network                     | `172.20.10.0/24` isolated virtual network |

# Objective

The objectives of this Incident-Response exercise were to:

- Generate and identify suspicious SSH authentication activity.
- Confirm that the Ubuntu endpoint was successfully logging SSH events.
- Verify that the Wazuh Agent was operational.
- Confirm that SSH events were being forwarded to the Wazuh Manager.
- Investigate Wazuh authentication alerts.
- Identify the source IP associated with the suspicious activity.
- Determine how Wazuh classified the authentication failures.
- Validate Wazuh Active Response.
- Confirm that the firewall-drop response was triggered.
- Review successful authentication events following the incident.
- Document the incident from a SOC analyst perspective.

# Preparation and Service Validation

Before investigating the security event, the SSH service on the Ubuntu endpoint was verified:

sudo systemctl status ssh --no-pager

The service was reported as:

Active: active (running)

The SSH service had been running since:

Mon 2026-08-10 21:56:08 UTC

The service validation confirmed that OpenSSH was operational.

The SSH listener was then verified using:

sudo ss -tulpn | grep ':22'

The output showed:

tcp   LISTEN   0   4096   0.0.0.0:22   0.0.0.0:*   users:(("sshd"...))
tcp   LISTEN   0   4096   [::]:22      [::]:*      users:(("sshd"...))

This confirmed that SSH was listening on TCP port 22 for both IPv4 and IPv6 connections.

# Wazuh Agent Validation

The Wazuh Agent on the Ubuntu endpoint was verified using:

sudo systemctl status wazuh-agent --no-pager

The service reported:

Active: active (running)

The running Wazuh processes included:

- wazuh-execd
- wazuh-agentd
- wazuh-syscheckd
- wazuh-logcollector
- wazuh-modulesd

The Agent reported:

Starting Wazuh v4.14.7...

and completed startup successfully.

This confirmed that the endpoint's Wazuh monitoring components were operational during the investigation.

Wazuh Manager Validation

The Wazuh Manager running on Kali Linux was also verified:

sudo systemctl status wazuh-manager --no-pager

The service reported:

Active: active (running)

The Manager processes included:

- wazuh-analysisd
- wazuh-remoted
- wazuh-logcollector
- wazuh-syscheckd
- wazuh-modulesd
- wazuh-execd

This confirmed that the central Wazuh monitoring and analysis platform was operational.

# Initial Security Event

The Ubuntu endpoint recorded suspicious SSH authentication activity involving a nonexistent account:

Aug 12 05:57:24 ubuntu-agent sshd[35330]:
Failed password for invalid user wronguser
from 172.20.10.4 port 42506 ssh2

The source IP was:

172.20.10.4

The attempted username was:

wronguser

The event demonstrated an authentication attempt against an account that did not exist on the Ubuntu endpoint.

The SSH session subsequently generated additional authentication failures:

PAM 2 more authentication failures

followed by:

Connection closed by invalid user wronguser
172.20.10.4 port 42506 [preauth]

Wazuh Detection

The event was successfully detected by Wazuh and associated with Rule 5710:

sshd: Attempt to login using a non-existent user

The alert was assigned Level 5.

Wazuh associated the event with:

- T1110.001 — Password Guessing
- T1021.004 — SSH

The alert contained:

agent:
    id: 004
    name: ubuntu-agent
    ip: 172.20.10.5

manager:
    name: kali

data:
    srcip: 172.20.10.4
    srcuser: wronguser

This provided the key information required for initial incident identification:

Source: 172.20.10.4
Target: 172.20.10.5
Account: wronguser
Service: SSH
Detection Rule: 5710

# Authentication Failure Escalation

Repeated authentication failures caused Wazuh to generate Rule 2502:

syslog: User missed the password more than one time

The rule was assigned Level 10.

Wazuh classified the event under:

T1110 — Brute Force

The alert contained:

firedtimes: 5

This demonstrated that the authentication failures had progressed beyond a single unsuccessful login attempt and met the conditions of Wazuh's brute-force detection rule.

## Automated Active Response

Following the Level 10 authentication-failure alert, Wazuh triggered an Active Response.

The response was recorded under Rule 651:

Host Blocked by firewall-drop Active Response

The corresponding active-response event showed:

program:
    active-response/bin/firewall-drop

command:
    add

# The response was associated with the alert generated by Rule 2502.

The source IP involved was:

172.20.10.4

This demonstrated an automated containment action in which Wazuh invoked its firewall-drop Active Response mechanism against the source associated with the detected activity.

# Active Response Evidence

The Wazuh Active Response log recorded the response:

2026/08/12 05:57:25
active-response/bin/firewall-drop

The response contained:

"command":"add"

and referenced the Level 10 brute-force alert:

"rule":{
    "level":10,
    "description":
    "syslog: User missed the password more than one time",
    "id":"2502"
}

This provided direct evidence that the detection event resulted in an automated response.

# Wazuh Investigation

The incident was investigated from the Wazuh Manager using:

sudo grep -iE "sshd|authentication|wronguser|172.20.10.4" \
/var/ossec/logs/alerts/alerts.log | tail -n 30

The investigation showed multiple SSH-related alerts, including:

- Authentication failures.
- Authentication successes.
- SSH session openings.
- SSH session closures.
- Active Response events.

This demonstrated the value of centralized SIEM data during an incident investigation.

Instead of relying exclusively on the endpoint's local logs, the SOC analyst could correlate multiple events from the Wazuh Manager.

## Authentication Success After the Incident

Later in the investigation, Wazuh recorded a successful SSH authentication:

Accepted password for kali from 172.20.10.4
port 42504 ssh2

Wazuh associated this event with Rule 5715:

sshd: authentication success.

The rule was assigned Level 3.

The event was followed by Rule 5501:

PAM: Login session opened.

This confirmed that a legitimate authenticated SSH session was successfully established.

The event also demonstrated that Wazuh could distinguish between failed authentication activity and successful authentication activity.

# SSH Session Closure

Wazuh later recorded:

pam_unix(sshd:session):
session closed for user kali

This was associated with Rule 5502:

PAM: Login session closed.

The session lifecycle could therefore be reconstructed from the centralized Wazuh events:

Authentication
      ↓
Session Opened
      ↓
SSH Activity
      ↓
Session Closed

# Additional SSH Event

Another successful SSH authentication was recorded:

Accepted password for kali from 172.20.10.5
port 52404 ssh2

This was also detected by Wazuh Rule 5715 and followed by Rule 5501 for the session opening.

This demonstrated that Wazuh continued collecting and analyzing SSH authentication activity after the initial incident.

# Incident Timeline

| Time     | Event                                       | Wazuh Evidence |
| -------- | ------------------------------------------- | -------------- |
| 05:57:24 | Failed SSH authentication using `wronguser` | Rule 5710      |
| 05:57:25 | Multiple authentication failures            | Rule 2502      |
| 05:57:25 | Automated firewall response triggered       | Rule 651       |
| 06:05:59 | Successful SSH authentication for `kali`    | Rule 5715      |
| 06:05:59 | SSH session opened                          | Rule 5501      |
| 06:08:09 | Additional successful SSH authentication    | Rule 5715      |
| 06:08:09 | SSH session opened                          | Rule 5501      |
| 07:55:17 | SSH session closed                          | Rule 5502      |
| 07:55:43 | SSH connection timeout recorded             | Rule 5742      |

# Detection and Response Chain

The most important part of this exercise was the complete detection-to-response chain:

Suspicious SSH Authentication
            │
            ▼
      Wazuh Rule 5710
   Non-existent user login
            │
            ▼
     Repeated Failures
            │
            ▼
      Wazuh Rule 2502
       Level 10 Alert
       Brute Force
            │
            ▼
       Active Response
            │
            ▼
        Rule 651
      firewall-drop
            │
            ▼
        Containment
            │
            ▼
    Continued Monitoring
            │
            ▼
      Authentication
       Success/Failure
            │
            ▼
      Session Tracking

This represents a practical SOC workflow in which detection is connected directly to an automated containment mechanism.

# Investigation Methodology

The investigation followed a layered approach.

1. Validate the SSH service
sudo systemctl status ssh --no-pager

2. Verify TCP/22 listener
sudo ss -tulpn | grep ':22'

3. Validate Wazuh Agent
sudo systemctl status wazuh-agent --no-pager

4. Validate Wazuh Manager
sudo systemctl status wazuh-manager --no-pager

5. Review endpoint authentication logs
sudo tail -n 30 /var/log/auth.log

6. Search centralized Wazuh alerts
sudo grep -iE "sshd|authentication|wronguser|172.20.10.4" \
/var/ossec/logs/alerts/alerts.log | tail -n 30

7. Review structured Wazuh alerts
sudo grep -iE "sshd|wronguser|Failed password|Accepted password" \
/var/ossec/logs/alerts/alerts.json | tail -n 10

8. Analyze the Active Response

The investigation identified:

Rule 2502 → Level 10 → Brute Force
        ↓
Rule 651 → firewall-drop Active Response

# Results

| Investigation Area            | Result                        |
| ----------------------------- | ----------------------------- |
| SSH service                   | Active                        |
| TCP/22 listener               | Confirmed                     |
| Wazuh Agent                   | Active                        |
| Wazuh Manager                 | Active                        |
| Suspicious SSH activity       | Detected                      |
| Invalid user                  | `wronguser`                   |
| Source IP                     | `172.20.10.4`                 |
| Initial detection             | Rule 5710                     |
| Brute-force detection         | Rule 2502                     |
| Alert severity                | Level 10                      |
| MITRE classification          | T1110 / T1110.001 / T1021.004 |
| Active Response               | Triggered                     |
| Response mechanism            | `firewall-drop`               |
| Containment evidence          | Rule 651                      |
| Successful SSH authentication | Detected                      |
| SSH session opening           | Detected                      |
| SSH session closure           | Detected                      |

# Incident-Response Significance

This exercise demonstrates several important SOC capabilities.

## Detection

Wazuh successfully identified suspicious SSH authentication activity and distinguished an attempt involving a nonexistent user.

## Correlation

Repeated authentication failures were escalated to a higher-severity brute-force detection.

## Automated Containment

Wazuh automatically invoked the firewall-drop Active Response after the Level 10 authentication failure condition was reached.

## Investigation

The incident was investigated using both endpoint authentication logs and centralized Wazuh alert data.

## Evidence Collection

The alerts contained useful investigative information including:

- Source IP.
-  username.
- Target agent.
- Detection rule.
- Alert level.
- MITRE ATT&CK mappings.
- Full log message.
- Active Response information.
- Recovery Validation

Subsequent successful SSH authentication and session events demonstrated that legitimate SSH activity could be observed after the initial security event.

# Lessons Learned
- A SOC investigation should begin by validating the underlying service and monitoring infrastructure.
- Authentication logs provide important evidence during SSH investigations.
- Wazuh can distinguish between failed and successful SSH authentication events.
- Repeated authentication failures can be escalated into higher-severity security alerts.
- Wazuh Active Response can automatically initiate containment actions based on detected activity.
- Centralized SIEM alerts make it easier to correlate multiple events from an endpoint.
- MITRE ATT&CK mappings provide useful context for understanding detected behaviour.
- Incident investigation should preserve evidence from both the original log source and the SIEM.
- Automated containment should be validated rather than assumed.
- Legitimate authentication events should also be monitored because they provide important context during an investigation.
- A complete SOC workflow extends beyond detection to analysis, containment, validation, and documentation.

# Evidence

Screenshots documenting this exercise are available:

![Wazuh Agent status](docs/screenshots/wazuh-agent-status.png)

![Wazuh Manager status](docs/screenshots/wazuh-manager-status.png)

![Failed wronguser authentication event](docs/screenshots/failed-wronguser-authentication-event..png)

![Successful SSH authentication](docs/screenshots/successful-ssh-login.png)

![Wazuh authentication-success alert](docs/screenshots/wazuh-authentication-success-alert.png)


docs/06-Incident-Response/screenshots/

# Conclusion

The SSH brute-force detection and response exercise was successfully completed.

Suspicious SSH authentication activity involving the nonexistent wronguser account was detected on the Ubuntu endpoint. Wazuh identified the activity using Rule 5710 and subsequently escalated repeated authentication failures to Rule 2502, a Level 10 brute-force detection.

Wazuh then triggered its firewall-drop Active Response, providing evidence of an automated containment action through Rule 651.

The investigation was performed using endpoint authentication logs and centralized Wazuh alerts. Subsequent successful authentication and SSH session events were also captured, demonstrating continued monitoring of the endpoint.

The exercise strengthens the lab's demonstration of:

- SOC monitoring
- SSH security monitoring
- Authentication-event analysis
- SIEM alert investigation
- Brute-force detection
- Automated incident response
- Firewall-based containment
- MITRE ATT&CK analysis
- Evidence-based investigation
- Incident documentation
