# Overview

This exercise verifies the effectiveness of Wazuh's firewall-drop Active Response mechanism in automatically containing a source IP associated with repeated SSH authentication failures.

# Objective

To confirm that Wazuh can:

- Detect repeated SSH authentication failures.
- Trigger the firewall-drop Active Response.
- Contain the offending source IP.
- Record the containment event.
- Automatically remove the temporary block.
- Provide verifiable evidence of both blocking and unblocking.

# Environment

| Component       | Details                              |
| --------------- | ------------------------------------ |
| Wazuh Manager   | Kali                                 |
| Wazuh Agent     | `ubuntu-agent`                       |
| Agent IP        | `172.20.10.5`                        |
| Source IP       | `172.20.10.4`                        |
| Detection Rule  | `2502`                               |
| Detection       | Repeated SSH authentication failures |
| MITRE ATT&CK    | `T1110 – Brute Force`                |
| Active Response | `firewall-drop`                      |

# Procedure

Repeated SSH authentication failures originating from 172.20.10.4 were generated against the Ubuntu agent.

Wazuh detected the activity using Rule 2502:

syslog: User missed the password more than one time

The Active Response log subsequently showed that firewall-drop was invoked with an add command:

2026/08/12 05:51:04 active-response/bin/firewall-drop:
"command":"add"

The response identified the offending source IP as:

"srcip":"172.20.10.4"

# Active Response Verification

The Active Response mechanism performed a key check for the source IP:

"command":"check_keys"
"keys":["172.20.10.4"]

This was followed by:

"command":"continue"

Wazuh subsequently generated Rule 651:

Host Blocked by firewall-drop Active Response

This confirms that the containment action was successfully registered by Wazuh.

Automatic Unblocking Verification

The Active Response log later recorded a delete command:

2026/08/12 05:56:05 active-response/bin/firewall-drop:
"command":"delete"

A firewall-drop delete command was subsequently recorded, indicating that the temporary Active Response was removed. Final firewall checks confirmed that no rule for 172.20.10.4 remained. Direct evidence of the Rule 652 alert was not captured and therefore remains pending verification.

# Firewall Verification

The firewall state was checked using:

sudo iptables -L -n --line-numbers

The output showed empty INPUT, FORWARD, and OUTPUT rule chains with an ACCEPT policy.

The nftables configuration was also checked:

sudo nft list ruleset

No rule referencing 172.20.10.4 was present.

Targeted searches were also performed:

sudo iptables -L -n --line-numbers | grep 172.20.10.4
sudo nft list ruleset | grep -C 3 172.20.10.4

Both returned no matching firewall rule.

# Findings

The evidence demonstrates that:

- Wazuh detected repeated SSH authentication failures from 172.20.10.4.
- Rule 2502 classified the activity as a brute-force-related authentication event.
- The firewall-drop Active Response was triggered.
- Wazuh recorded a successful containment event through Rule 651.
- The source IP 172.20.10.4 was processed by the Active Response through the check_keys and continue stages.
- A subsequent firewall-drop delete operation was executed.
- Final iptables and nftables checks showed no remaining firewall rule for 172.20.10.4.
- Direct evidence of Rule 652 — Host Unblocked by firewall-drop Active Response — was not captured and remains pending verification.

# Result

PASS – Active Response containment successfully verified.

The test confirmed that Wazuh successfully detected repeated SSH authentication failures and invoked the firewall-drop Active Response against 172.20.10.4.

The evidence confirms the blocking process through Rule 2502, the firewall-drop add operation, the check_keys and continue stages, and Rule 651, which recorded the successful host-blocking action.

A subsequent firewall-drop delete operation was also recorded, and final iptables and nftables checks showed that no firewall rule for 172.20.10.4 remained.

However, Rule 652 was not directly captured in the submitted evidence. Therefore, the automatic removal of the firewall rule is supported by the delete event and final firewall verification, but the specific Wazuh unblocking alert remains pending direct evidence.

# Evidence

The following screenshots shows evidence for the exercise:


![Firewall-Drop Add and Delete Event](docs/screenshots/firewall-drop-add-and-delete-check-keys-events.png)

![Check Keys and Continue Events](docs/screenshots/check-keys-firewall-drop-add-and-delete-events.png)

![Rule 651 – Host Blocked by firewall-drop Active Response](docs/screenshots/rule-651-host-blocked-by-firewall-drop-active-response.png)
![Rule 651 – Host Blocked by firewall-drop Active Response](docs/screenshots/rule-651-host-blocked-firewall-drop-active-response.png)

![Final iptables and nftables output](docs/screenshots/final-iptables- nftables-output.png)


# Conclusion

Exercise 2 successfully demonstrated Wazuh's ability to detect repeated SSH authentication failures and automatically initiate the firewall-drop Active Response against the identified source IP, 172.20.10.4.

The evidence confirmed the detection of Rule 2502, execution of the firewall-drop add command, processing of the source IP through the check_keys and continue stages, and the generation of Rule 651, confirming that the host was blocked.

A subsequent firewall-drop delete command was also recorded, and the final firewall verification showed that no rule for 172.20.10.4 remained in iptables or nftables. This is consistent with the temporary containment being removed.

However, Rule 652 – Host Unblocked by firewall-drop Active Response – was not directly captured in the submitted evidence. Therefore, the blocking/containment portion of the exercise is fully verified, while the removal of the firewall rule is supported by the recorded delete event and final firewall checks. Direct verification of the Rule 652 unblocking alert remains pending.

