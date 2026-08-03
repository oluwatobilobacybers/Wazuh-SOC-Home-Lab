# Wazuh SIEM Installation Guide

## Objective

Deploy a Security Information and Event Management (SIEM) platform using Wazuh to monitor endpoints, detect security events, and perform incident analysis.

---

## Lab Environment

| Component | Details |
|-----------|---------|
| Hypervisor | VirtualBox |
| SIEM | Wazuh 4.x |
| Dashboard | Wazuh Dashboard |
| Operating System | Ubuntu Server |
| Monitoring Agent | Wazuh Agent |
| Attacker Machine | Kali Linux |
| Target Machine | Metasploitable 2 |

---

## Installation Steps

### Step 1

Updated Ubuntu Server.

```bash
sudo apt update
sudo apt upgrade -y
```

### Step 2

Downloaded the Wazuh installer.

```bash
curl -sO https://packages.wazuh.com/4.8/wazuh-install.sh
```

### Step 3

Executed the installation.

```bash
sudo bash wazuh-install.sh -a
```

### Step 4

Verified installation.

```bash
systemctl status wazuh-manager
```

### Step 5

Logged into the dashboard.

```
https://<Server-IP>
```

---

## Result

The Wazuh Dashboard was successfully deployed.

The Manager, Indexer and Dashboard services were operational.

Endpoints were successfully connected to the SIEM platform.

---

## Skills Demonstrated

- Ubuntu Administration
- SIEM Deployment
- Wazuh Installation
- Linux Administration
- Security Monitoring
