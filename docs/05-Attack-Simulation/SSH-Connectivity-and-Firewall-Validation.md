# SSH Connectivity and Firewall Validation

## Overview

This exercise focused on validating network connectivity between the Kali Linux Wazuh server and the monitored Ubuntu endpoint, while investigating a firewall rule that was blocking traffic from the Kali system.

The exercise demonstrates a practical troubleshooting workflow:

**Identify → Inspect → Test → Troubleshoot → Modify → Validate**

The exercise was performed within the isolated Wazuh SOC home laboratory environment.

---

# Lab Environment

| Component | Details |
|---|---|
| Source System | Kali Linux |
| Source IP | `172.20.10.4` |
| Destination System | Ubuntu Server 24.04.4 LTS |
| Destination IP | `172.20.10.5` |
| Protocol | SSH |
| Destination Port | TCP/22 |
| Firewall | iptables / nftables |
| Network | `172.20.10.0/24` isolated virtual network |

---

# Objective

The objectives of this exercise were to:

- Inspect the Ubuntu endpoint firewall configuration.
- Identify firewall rules affecting traffic from the Kali system.
- Validate SSH connectivity to the Ubuntu endpoint.
- Determine whether TCP port 22 was reachable.
- Perform detailed SSH troubleshooting using verbose logging.
- Verify successful SSH authentication.
- Remove the blocking firewall rule.
- Validate the resulting firewall state.
- Confirm successful SSH connectivity after the firewall change.

---

# Initial Firewall Investigation

The Ubuntu endpoint was inspected using:

```bash
sudo iptables -L -n --line-numbers
```

The initial output showed:

Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    DROP       0    --  172.20.10.4          0.0.0.0/0

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination
1    DROP       0    --  172.20.10.4          0.0.0.0/0

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination

The important rule was:

DROP  0  --  172.20.10.4  0.0.0.0/0

This indicated that traffic originating from the Kali/Wazuh server at 172.20.10.4 was being dropped by the Ubuntu endpoint.

# nftables Verification

The firewall state was independently inspected using:

```bash
sudo nft list ruleset
```

The output confirmed the corresponding rule:

table ip filter {
    chain INPUT {
        type filter hook input priority filter; policy accept;
        ip saddr 172.20.10.4 counter packets 54 bytes 3152 drop
    }

    chain FORWARD {
        type filter hook forward priority filter; policy accept;
        ip saddr 172.20.10.4 counter packets 0 bytes 0 drop
    }
}

The INPUT rule contained a packet counter:

counter packets 54 bytes 3152

This provided evidence that traffic from 172.20.10.4 had actually matched the firewall rule.

# SSH Port Connectivity Test

From the Kali system, TCP port 22 was tested using Netcat:

```bash
nc -vz 172.20.10.5 22
```

The result was:

172.20.10.5: inverse host lookup failed: Unknown host
(UNKNOWN) [172.20.10.5] 22 (ssh) open

The reverse DNS lookup failure was not an SSH connectivity failure.

The important result was:

22 (ssh) open

This confirmed that TCP port 22 on the Ubuntu endpoint was reachable and that an SSH service was listening.

# Detailed SSH Troubleshooting

A verbose SSH connection was initiated from Kali:

```bash
ssh -vvv kali@172.20.10.5
```

The SSH client reported:

debug1: Connecting to 172.20.10.5 [172.20.10.5] port 22.
debug1: Connection established.

This confirmed successful TCP connectivity.

The remote system identified itself as:

OpenSSH_9.6p1 Ubuntu-3ubuntu13.18

The SSH key exchange was successfully completed.

The server host key was also verified:

debug1: Host '172.20.10.5' is known and matches the ED25519 host key.

# SSH Authentication

The SSH server offered:

Authentications that can continue: publickey, password

No local private SSH keys were available on the Kali system, so password authentication was attempted.

Authentication succeeded:

Authenticated to 172.20.10.5 ([172.20.10.5]:22) using "password".

An interactive SSH session was then established.

The Ubuntu endpoint displayed:

Welcome to Ubuntu 24.04.4 LTS

The remote shell was successfully obtained:

kali@ubuntu-agent:~$

# Firewall Rule Validation

After the firewall troubleshooting process, the INPUT chain was checked again:

sudo iptables -L INPUT -n --line-numbers

The resulting configuration was:

Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination

The previous DROP rule for 172.20.10.4 was no longer present.

This confirmed that the previously observed DROP rule for 172.20.10.4 was no longer present in the INPUT chain.

# Final SSH Validation

A normal SSH connection was then initiated:

ssh kali@172.20.10.5

The connection succeeded and the Ubuntu login banner was displayed:

Welcome to Ubuntu 24.04.4 LTS

The session successfully reached:

kali@ubuntu-agent:~$

The Ubuntu system also recorded the connection:

Last login: Wed Aug 12 06:06:00 2026 from 172.20.10.4

This provided additional confirmation that the Ubuntu endpoint successfully accepted an SSH connection originating from the Kali system.

# Troubleshooting Methodology

The exercise followed a layered troubleshooting approach.

```bash
1. Inspect the firewall
sudo iptables -L -n --line-numbers

2. Verify the underlying firewall configuration
sudo nft list ruleset

3. Test the service port
nc -vz 172.20.10.5 22

4. Perform detailed protocol troubleshooting
ssh -vvv kali@172.20.10.5

5. Authenticate and establish a session
ssh kali@172.20.10.5

6. Re-check the firewall
sudo iptables -L INPUT -n --line-numbers

7. Validate the final state

Successful SSH login confirmed that the connectivity problem had been resolved.
```

# Results

| Test                     | Result                              |
| ------------------------ | ----------------------------------- |
| Firewall rule inspection | Blocking rule identified            |
| nftables verification    | Blocking rule confirmed             |
| Firewall counter         | Traffic from `172.20.10.4` observed |
| TCP/22 connectivity      | Open                                |
| SSH protocol negotiation | Successful                          |
| Host key verification    | Successful                          |
| Password authentication  | Successful                          |
| Interactive SSH session  | Successful                          |
| Blocking INPUT rule      | Removed                             |
| Final SSH validation     | Successful                          |

# Security Significance

This exercise demonstrates an important SOC and security engineering principle:

A service being available does not necessarily mean that network access to the service is correctly configured.

Firewall rules can intentionally or unintentionally prevent legitimate administrative communication.

The investigation therefore required validation at multiple layers:

Network
   ↓
TCP Port
   ↓
SSH Protocol
   ↓
Authentication
   ↓
Interactive Session

The exercise also demonstrated the importance of checking both configuration and runtime behaviour rather than assuming that a configuration change has worked.

# Lessons Learned

- Firewall rules should be inspected when network connectivity behaves unexpectedly.
- iptables and nftables can provide valuable evidence during Linux firewall troubleshooting.
- Firewall packet counters can help determine whether traffic is actually matching a rule.
- Netcat provides a quick way to determine whether a TCP service is reachable.
- SSH verbose mode (-vvv) provides detailed information about connection establishment, key exchange, authentication, and session creation.
- Successful TCP connectivity does not automatically guarantee successful authentication.
- Successful authentication does not necessarily mean the complete administrative workflow is functioning; an interactive session should also be verified.
- Security controls should be validated after configuration changes.
- Troubleshooting should proceed from the lowest network layer toward the application layer.
- Documenting both the failure state and successful recovery provides stronger evidence of practical troubleshooting ability.

# Evidence

The following evidence was captured during the exercise:

### Firewall Rule Inspection
- Initial `iptables` output showing the DROP rule for `172.20.10.4`.
- `nftables` output confirming the corresponding DROP rule and packet counter.

### TCP Connectivity
- `nc -vz 172.20.10.5 22` confirming TCP/22 was reachable.

### SSH Troubleshooting
- `ssh -vvv kali@172.20.10.5` showing:
  - TCP connection establishment
  - SSH protocol negotiation
  - Host key verification
  - Authentication methods
  - Successful password authentication
  - Interactive session establishment

### Firewall Remediation
- Final `iptables -L INPUT -n --line-numbers` output showing that the DROP rule was no longer present.

### Final Validation
- Successful `ssh kali@172.20.10.5` connection.
- Ubuntu login record showing the connection originated from `172.20.10.4`.

## Screenshots.

## iptables and nftables Verification

![nftables and iptables firewall rule listing](docs/screenshots/nftables-iptables-rule.png)

## SSH Verbose Troubleshooting

![SSH connection initiated](docs/screenshots/ssh-connection-initiated.png)

## Final SSH Validation

![Successful final SSH login](docs/screenshots/successful-ssh-login.png)



# Conclusion

The SSH connectivity and firewall validation exercise was successfully completed.

A firewall rule affecting traffic from the Kali Wazuh server (172.20.10.4) was identified and investigated on the Ubuntu monitored endpoint (172.20.10.5).

TCP/22 connectivity was tested, SSH negotiation was examined using verbose logging, password authentication was successfully completed, and an interactive Ubuntu session was established.

The blocking firewall rule was subsequently removed and the firewall configuration was re-validated.

The final SSH test confirmed successful TCP connectivity, SSH protocol negotiation, password authentication, and establishment of an interactive session between the Kali system and Ubuntu endpoint.
This exercise strengthens the lab's demonstration of:

- Linux firewall administration
- Network troubleshooting
- SSH administration
- Endpoint security validation
- Security control verification
- Evidence-based troubleshooting
- SOC operational documentation

