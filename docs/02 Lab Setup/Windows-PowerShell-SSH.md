# Windows PowerShell SSH Remote Administration

## Objective

This document describes the configuration and use of Windows PowerShell Secure Shell (SSH) to remotely administer the Ubuntu Server hosting the Wazuh Agent within the Wazuh SOC Home Lab.

Using SSH significantly improved administrative efficiency by providing a reliable command-line interface, eliminating the need to rely on the VirtualBox console for routine management tasks.

---

# Environment

| Component | Details |
|----------|---------|
| Host Operating System | Windows 11 Pro |
| Remote Operating System | Ubuntu Server 24.04 LTS |
| SSH Client | Windows PowerShell (OpenSSH Client) |
| SSH Server | OpenSSH Server (Ubuntu) |
| Communication Protocol | SSH |
| Default Port | 22/TCP |

---

# Purpose

Initially, all administrative tasks were performed directly through the VirtualBox console.

Although functional, this approach presented several limitations:

- Slow navigation
- Limited terminal size
- Difficult copy-and-paste operations
- Reduced productivity when executing long commands
- Inconvenient documentation of terminal output

To improve efficiency, remote administration was implemented using Windows PowerShell over SSH.

This approach closely reflects enterprise Linux administration practices, where servers are managed remotely instead of through graphical consoles.

---

# Network Information

| Device | IP Address |
|---------|-----------|
| Windows Host | Local Machine |
| Ubuntu Server | 172.20.10.5 |
| SSH Port | 22 |

---

# Prerequisites

Before establishing the SSH connection, the following requirements were verified:

- Ubuntu Server installed
- OpenSSH Server installed
- SSH service running
- Windows OpenSSH Client available
- Network connectivity between Windows and Ubuntu

---

# Step 1 — Verify Ubuntu IP Address

Determine the IP address assigned to the Ubuntu Server.

```bash
hostname -I
```

Example output:

```text
172.20.10.5
```

---

# Step 2 — Verify SSH Service

Confirm that the SSH service is running.

```bash
sudo systemctl status ssh
```

Expected output:

```text
Active: active (running)
```

If necessary, start the service:

```bash
sudo systemctl start ssh
```

Enable SSH to start automatically after reboot:

```bash
sudo systemctl enable ssh
```

---

# Step 3 — Test Network Connectivity

From the Windows host, verify connectivity to the Ubuntu Server.

```powershell
ping 172.20.10.5
```

Successful replies confirmed Layer 3 connectivity.

---

# Step 4 — Establish SSH Session

Open Windows PowerShell and connect to the Ubuntu Server.

```powershell
ssh kali@172.20.10.5
```

When prompted:

```text
Are you sure you want to continue connecting (yes/no)?
```

Enter:

```text
yes
```

Provide the Ubuntu user's password.

Successful authentication presents the Ubuntu shell.

Example:

```text
kali@ubuntu-agent:~$
```

---

# Administrative Advantages

Using SSH provided several operational benefits:

- Faster command execution
- Full terminal copy-and-paste support
- Easier troubleshooting
- Improved documentation
- Remote server administration
- Better alignment with enterprise Linux administration

Most project commands throughout this repository were executed through this remote SSH session.

---

# Common Administrative Commands

Display hostname:

```bash
hostname
```

Check operating system:

```bash
lsb_release -a
```

Display IP address:

```bash
hostname -I
```

Restart a service:

```bash
sudo systemctl restart ssh
```

Check running services:

```bash
sudo systemctl status ssh
```

---

# Challenges Encountered

Initially, administration was performed exclusively through the VirtualBox console.

This created several operational challenges:

- Copying commands from documentation was cumbersome.
- Capturing terminal output for GitHub documentation required manual transcription.
- Long troubleshooting sessions became inefficient.

Migrating to PowerShell SSH resolved these issues and became the primary administration method for the Ubuntu endpoint.

---

# Verification

Successful remote administration was confirmed by:

- Establishing an SSH session
- Executing Linux administrative commands remotely
- Installing software
- Editing configuration files
- Restarting services
- Monitoring Wazuh logs
- Performing troubleshooting without using the VirtualBox console

---

# Lessons Learned

- SSH is the preferred method for administering Linux servers in enterprise environments.
- Remote administration significantly improves operational efficiency.
- PowerShell provides a reliable SSH client on Windows without requiring additional software.
- Proper SSH configuration simplifies documentation, troubleshooting, and system management throughout a cybersecurity project.

---

# Screenshots


```text
![Windows PowerShell SSH login](Screenshots/powershell-ssh-login.png)

![Successful Ubuntu terminal session](Screenshots/successful-buntu terminal-session.png)

![Ping verification](Screenshots/ping-verification.png)
```

---

# References

OpenSSH Documentation

https://www.openssh.com/

Ubuntu SSH Documentation

https://ubuntu.com/server/docs/service-openssh
