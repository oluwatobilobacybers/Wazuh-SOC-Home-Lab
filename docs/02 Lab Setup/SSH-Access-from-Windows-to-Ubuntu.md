# SSH Access from Windows to Ubuntu Server

## Objective

This document records the configuration of secure remote administration from the Windows 11 host to the Ubuntu Server used as the Wazuh Agent endpoint.

The Ubuntu Server was deployed as a virtual machine using **Oracle VirtualBox 7.2.6**. SSH access was configured using the built-in Windows PowerShell OpenSSH client.

This configuration provided a more efficient method of administering the Ubuntu endpoint without relying exclusively on the VirtualBox console.

---

# Environment

| Component | Configuration |
|-----------|---------------|
| Host OS | Windows 11 Pro |
| Virtualization | Oracle VirtualBox 7.2.6 |
| Guest OS | Ubuntu Server 24.04 LTS |
| SSH Client | Windows PowerShell / OpenSSH Client |
| SSH Server | OpenSSH Server |
| Protocol | SSH |
| Default Port | TCP/22 |
| Ubuntu IP | 172.20.10.5 |

---

# Purpose

Initially, administrative tasks were performed directly through the VirtualBox console.

Although functional, this introduced several limitations:

- Difficult command copy-and-paste
- Limited terminal usability
- Increased risk of typing errors
- Difficult documentation of long commands
- Frequent switching between documentation and the virtual machine
- Less efficient troubleshooting

SSH provided a more efficient administrative interface and allowed the Ubuntu endpoint to be managed remotely from the Windows host.

This workflow also reflects common enterprise practices for remotely administering Linux servers.

---

# Prerequisites

The following requirements were verified:

- Ubuntu Server installed
- Ubuntu Server network connectivity established
- OpenSSH Server installed
- SSH service running
- Windows OpenSSH Client available
- Windows host able to communicate with Ubuntu

---

# Step 1 — Verify Ubuntu IP Address

The Ubuntu Server IP address was identified using:

```bash
hostname -I

Example:

172.20.10.5

The address was subsequently used as the SSH destination from Windows.

# Step 2 — Install OpenSSH Server

If OpenSSH Server was not already installed, it was installed using:
sudo apt update
sudo apt install openssh-server -y

# Step 3 — Verify SSH Service

The SSH service was checked using:
sudo systemctl status ssh

A healthy service should display:
Active: active (running)

The service was also configured to start automatically after system reboot:
sudo systemctl enable ssh

# Step 4 — Verify SSH Listening Port

The SSH listening socket was verified using:
sudo ss -tlnp | grep :22

Expected output includes:
LISTEN ... 0.0.0.0:22

This confirmed that the Ubuntu Server was listening for SSH connections on TCP port 22.

# Step 5 — Test Network Connectivity

Before establishing the SSH session, connectivity between the Windows host and Ubuntu Server was verified.

From Windows PowerShell:
ping 172.20.10.5

Successful replies confirmed basic network connectivity between the host and the Ubuntu virtual machine.

# Step 6 — Establish SSH Connection

Windows PowerShell was used as the SSH client.

The Ubuntu Server was accessed with:
ssh kali@172.20.10.5

The first connection displayed a host authenticity warning similar to:
The authenticity of host '172.20.10.5' can't be established.

The connection was accepted by entering:
yes

The server's SSH host key was then stored in the Windows user's known_hosts file.

The Ubuntu user's password was subsequently entered to complete authentication.

# Successful SSH Session

After successful authentication, the Ubuntu shell became available remotely.

Example:
kali@ubuntu-agent:~$

This confirmed that Windows PowerShell had successfully established an SSH session with the Ubuntu Server.

# Remote Administration Workflow

After SSH access was established, PowerShell became the primary method used to administer the Ubuntu endpoint during the lab.

Activities performed through the SSH session included:

- Installing and updating packages
- Editing configuration files
- Managing system services
- Checking network configuration
- Monitoring Wazuh Agent logs
- Troubleshooting Wazuh connectivity
- Verifying agent communication
- Configuring File Integrity Monitoring
- Executing diagnostic commands
- Capturing terminal output for documentation

# Copy-and-Paste Workflow

One of the main reasons for configuring SSH was to improve command transfer between Windows documentation and Ubuntu.

The workflow became:

Windows Documentation
        |
        | Copy command
        v
Windows PowerShell
        |
        | SSH session
        v
Ubuntu Server
        |
        | Execute command
        v
Wazuh Agent / Linux System

This reduced manual typing and made it easier to execute long commands accurately.

It also improved the documentation process because command output could be copied directly from PowerShell.

# Troubleshooting

1. Connection Refused

Possible error:
ssh: connect to host 172.20.10.5 port 22: Connection refused

The SSH service was checked:
sudo systemctl status ssh

If necessary, the service was started:
sudo systemctl start ssh

# 2. SSH Service Not Running

The service was restarted when required:
sudo systemctl restart ssh

The status was then verified:
sudo systemctl status ssh

 3. Incorrect IP Address

The Ubuntu IP address was rechecked:
hostname -I

or:

ip a

The current IP address was then used for the SSH connection.

# 4. Firewall Restrictions

UFW status can be checked using:
sudo ufw status

If SSH traffic is being blocked:
sudo ufw allow 22/tcp

Firewall configuration should be restricted appropriately in production environments rather than broadly allowing unnecessary access.

# 5. Authentication Failure

When authentication failed, the following were verified:
Correct username
Correct password
Caps Lock status
Keyboard layout
Correct Ubuntu account

# Security Considerations

The lab operates within a controlled virtualized environment.

Password authentication was used for administrative access during the lab.

For production environments, SSH key-based authentication is preferred because it provides stronger authentication and can reduce exposure to password-based attacks.

Additional production hardening would include:
Disabling direct root SSH login
Restricting SSH access to trusted networks
Using SSH keys
Applying least privilege
Monitoring SSH authentication logs
Implementing appropriate firewall rules
Keeping OpenSSH patched

# Verification

SSH remote administration was considered successful after the following were confirmed:

Ubuntu Server was reachable from Windows
TCP/22 was accessible
SSH service was running
PowerShell successfully established an SSH session
Ubuntu commands could be executed remotely
Command output could be copied from the PowerShell session
Wazuh Agent administration could be performed remotely

# Outcome

SSH remote administration between the Windows 11 host and Ubuntu Server was successfully established.

The configuration significantly improved the efficiency of the Wazuh SOC Home Lab by providing reliable remote command-line access to the Ubuntu endpoint.

This became an important part of the lab's administration and troubleshooting workflow, particularly during Wazuh Agent configuration, connectivity troubleshooting, log analysis, and File Integrity Monitoring configuration.

# Lessons Learned

- SSH provides an efficient method for remotely administering Linux servers.
- Windows PowerShell includes a functional OpenSSH client without requiring third-party SSH software.
- Verifying network connectivity before troubleshooting SSH helps isolate connectivity issues.
- Service status and listening-port checks are useful first steps when troubleshooting SSH.
- Remote administration improves command accuracy, troubleshooting efficiency, and technical documentation.
- Enterprise Linux administration commonly relies on secure remote management rather than direct console access.

# Screenshots

![PowerShell SSH Login](Screenshots/powershell-ssh-login.png)

![Successful Ubuntu SSH Session](Screenshots/successful-ubuntu terminal-session.png)

![Verification](Screenshots/ubuntu-verification.png


# References

OpenSSH: https://www.openssh.com/
Ubuntu OpenSSH documentation: https://ubuntu.com/server/docs/service-openssh

