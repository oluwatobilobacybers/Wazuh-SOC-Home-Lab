# SSH Access from Windows to Ubuntu Server

## Objective

This document explains how secure remote administration of the Ubuntu Server was configured from the Windows 11 host using PowerShell and OpenSSH. The Ubuntu Server was running as a virtual machine under Oracle VirtualBox 7.2.6.

Remote SSH access significantly improved lab management by allowing commands to be executed from the Windows host without interacting directly with the Ubuntu virtual machine console.

---

# Environment

| Component | Value |
|-----------|------|
| Host OS | Windows 11 Pro |
| Guest OS | Ubuntu Server 24.04 LTS |
| Protocol | SSH |
| Client | Windows PowerShell |
| Port | 22/TCP |

---

# Why SSH Was Configured

Managing Ubuntu exclusively through the VirtualBox console was inefficient because

- Copying commands was difficult.
- Documentation required constant switching between windows.
- Long installation commands increased the risk of typing errors.
- Screenshots were harder to capture.

SSH provided a faster and more reliable administrative interface.

---

# Step 1 — Verify OpenSSH Server Installation

On Ubuntu:

```bash
sudo apt update
sudo apt install openssh-server -y
```

---

# Step 2 — Verify SSH Service Status

```bash
sudo systemctl status ssh
```

Expected output:

```
Active: active (running)
```

---

# Step 3 — Enable SSH at Startup

```bash
sudo systemctl enable ssh
```

---

# Step 4 — Verify Listening Port

```bash
sudo ss -tlnp | grep :22
```

Expected output:

```
LISTEN
0.0.0.0:22
```

---

# Step 5 — Determine Ubuntu IP Address

```bash
ip a
```

Example:

```
172.20.10.5
```

---

# Step 6 — Connect from Windows PowerShell

Open PowerShell:

```powershell
ssh kali@172.20.10.5
```

Example:

```
PS C:\Users\User> ssh kali@172.20.10.5
```

---

# Authentication

The first connection displayed:

```
The authenticity of host cannot be established.
```

Typing

```
yes
```

added the server fingerprint to the local known_hosts file.

Afterwards, Ubuntu prompted for the user's password.

---

# Successful Connection

After authentication:

```
kali@ubuntu-agent:~$
```

The Ubuntu terminal became available remotely through PowerShell.

---

# Benefits

SSH provided several advantages:

- Remote administration
- Faster troubleshooting
- Easy command copy-and-paste
- Reduced typing errors
- Better documentation workflow
- Easier screenshot collection

---

# Troubleshooting

## Connection Refused

Cause:

```
ssh: connect to host ... port 22 refused
```

Resolution:

```bash
sudo systemctl start ssh
```

---

## SSH Service Not Running

```bash
sudo systemctl status ssh
```

If inactive:

```bash
sudo systemctl restart ssh
```

---

## Wrong IP Address

Verify using:

```bash
ip a
```

The Ubuntu VM's network configuration or DHCP lease could result in a different IP address after reboot.

---

## Firewall Blocking SSH

Check UFW status:

```bash
sudo ufw status
```

Allow SSH:

```bash
sudo ufw allow 22/tcp
```

---

## Authentication Failure

Verify:

- Username
- Password
- Keyboard layout
- Caps Lock

---

# Security Considerations

This lab uses password authentication because it operates inside an isolated virtual network.

In production environments, SSH key authentication is recommended.

---

# Outcome

SSH connectivity between the Windows host and Ubuntu Server was successfully established.

This significantly improved administrative efficiency throughout the Wazuh SOC Home Lab deployment and troubleshooting process.
