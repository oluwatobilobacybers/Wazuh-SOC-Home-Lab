# Copy and Paste Between Windows and Ubuntu

## Objective

This document describes the methods evaluated for transferring commands and text between the Windows 11 host operating system and the Ubuntu Server virtual machine during the deployment of the Wazuh SOC Home Lab.

It also explains why Windows PowerShell over SSH was adopted as the primary administration method instead of relying on the Oracle VirtualBox console.

---

# Environment

| Component | Details |
|----------|---------|
| Host Operating System | Windows 11 Pro |
| Hypervisor | Oracle VirtualBox 7.2.6 |
| Guest Operating System | Ubuntu Server 24.04 LTS |
| Remote Access | Windows PowerShell (OpenSSH Client) |

---

# Background

During the initial stages of the project, all administrative tasks were performed directly through the Oracle VirtualBox console.

Although this approach allowed direct interaction with the Ubuntu Server, it introduced several workflow limitations that affected productivity during system configuration and troubleshooting.

---

# Problem Statement

The VirtualBox console was not suitable for prolonged Linux administration because it made transferring commands between documentation and the virtual machine inefficient.

Examples included:

- Difficulty copying long terminal commands
- Inconsistent clipboard synchronization
- Manual retyping of configuration files
- Increased risk of typing errors
- Slow documentation of troubleshooting procedures

These issues became more noticeable during the installation and configuration of Wazuh components.

---

# Initial Approach

Administration was performed directly inside the Oracle VirtualBox console.

Example:

```text
Oracle VirtualBox
        │
        ▼
Ubuntu Server Terminal
```

While functional, this approach required manually entering many commands and made documentation significantly slower.

---

# Alternative Solutions Considered

## Option 1 — VirtualBox Shared Clipboard

Oracle VirtualBox provides a Shared Clipboard feature that allows text to be copied between the host and guest operating systems.

Although available, this feature depends on Guest Additions and can be inconsistent, particularly on server installations that do not use a graphical desktop environment.

Result:

- Not selected as the primary administration method.

---

## Option 2 — Open VM Tools / Guest Additions

Installing Guest Additions or Open VM Tools improves integration between the host and guest operating systems.

Benefits include:

- Clipboard synchronization
- Drag-and-drop support
- Improved display handling

Since Ubuntu Server was deployed without a graphical desktop environment, these features offered limited operational value for this project.

Result:

- Considered unnecessary for a command-line focused SOC lab.

---

## Option 3 — Windows PowerShell SSH

Windows PowerShell includes an OpenSSH client capable of securely connecting to Linux servers.

Example:

```powershell
ssh kali@172.20.10.5
```

Advantages:

- Native Windows support
- Reliable copy-and-paste
- Remote administration
- Faster command execution
- Better terminal experience
- Easy capture of command output for documentation

Result:

- Adopted as the standard administration method.

---

# Selected Solution

Windows PowerShell SSH was chosen as the preferred administration interface for the Ubuntu Server.

The workflow became:

```text
Windows 11
        │
        │
PowerShell
        │
SSH (TCP/22)
        │
        ▼
Ubuntu Server
```

This approach mirrors enterprise Linux administration practices where servers are managed remotely rather than through virtualization consoles.

---

# Benefits Achieved

After migrating to SSH administration, the following improvements were observed:

- Commands could be copied directly from documentation.
- Terminal output could be copied into GitHub documentation.
- Configuration files were edited more efficiently.
- Troubleshooting sessions became faster.
- Administrative tasks no longer required interaction with the VirtualBox console.

This significantly improved the overall efficiency of the Wazuh deployment.

---

# Verification

Successful implementation was verified by:

- Establishing SSH connectivity
- Executing administrative commands remotely
- Editing configuration files
- Installing software packages
- Restarting Wazuh services
- Monitoring Wazuh logs
- Documenting command outputs without manual transcription

---

# Lessons Learned

Several methods exist for interacting with Linux virtual machines; however, not all are equally efficient for enterprise administration.

For command-line based cybersecurity laboratories, remote administration over SSH provides:

- Greater reliability
- Improved productivity
- Better documentation workflows
- Reduced configuration errors
- An experience closely aligned with real-world enterprise environments

This approach became the standard administration method for the remainder of the Wazuh SOC Home Lab project.

---

# Screenshots

Add the following screenshots:

### Oracle VirtualBox Console

```
docs/screenshots/virtualbox-console.png
```

---

### Windows PowerShell SSH Session

```
docs/screenshots/powershell-ssh-session.png
```

---

### Successful Remote Login

```
docs/screenshots/ubuntu-ssh-login.png
```

---

# References

Oracle VirtualBox Documentation

https://www.virtualbox.org/manual/

Microsoft OpenSSH Documentation

https://learn.microsoft.com/windows-server/administration/openssh/

Ubuntu OpenSSH Documentation

https://ubuntu.com/server/docs/service-openssh
