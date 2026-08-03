# Wazuh Installation Guide

## Objective

Deploy a single-node Wazuh SIEM environment capable of collecting logs, monitoring endpoints, detecting security events, and generating alerts for incident analysis.

---

# Lab Environment

| Component | Specification |
|-----------|---------------|
| Hypervisor | Oracle VirtualBox |
| Wazuh Server | Ubuntu Server 24.04 LTS |
| Analyst Workstation | Kali Linux |
| Victim Machine | Metasploitable 2 |
| SIEM Version | Wazuh 4.14.x |

---

# Network Topology

| Machine | IP Address | Role |
|----------|------------|------|
| Kali Linux | 172.20.10.4 | SOC Analyst |
| Ubuntu Server | 172.20.10.3 | Wazuh Server |
| Metasploitable2 | 172.20.10.x | Target System |

---

# Installation Procedure

## 1. Ubuntu Server Installation

- Installed Ubuntu Server 24.04 LTS
- Configured OpenSSH
- Assigned static IP address
- Updated all packages

```bash
sudo apt update
sudo apt upgrade -y
```

---

## 2. System Preparation

Installed required dependencies.

```bash
sudo apt install curl unzip wget apt-transport-https gnupg lsb-release ca-certificates software-properties-common -y
```

Configured kernel parameters.

```bash
sudo sysctl -w vm.max_map_count=262144
```

Made the configuration persistent.

```bash
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

---

## 3. Wazuh Installation

Downloaded the official installer.

```bash
curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
```

Executed the installer.

```bash
sudo bash wazuh-install.sh -a
```

---

## 4. Services Verification

Verified all services.

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
```

---

## 5. Agent Deployment

Installed the Wazuh agent on Ubuntu Server.

Imported the agent key generated from the Wazuh Manager.

Restarted the agent.

```bash
sudo systemctl restart wazuh-agent
```

Verified communication.

```bash
sudo /var/ossec/bin/agent_control -l
```

The agent appeared as **Active**.

---

# Verification

Verified:

- Wazuh Dashboard accessible
- Wazuh Manager running
- Wazuh Indexer running
- Wazuh Dashboard running
- Wazuh Agent connected
- File Integrity Monitoring operational

---

# Outcome

Successfully deployed a functional Wazuh SIEM lab capable of:

- Endpoint monitoring
- File Integrity Monitoring (FIM)
- Log collection
- Security event monitoring
- Alert generation
- Incident investigation

---

# Skills Demonstrated

- Ubuntu Server Administration
- Linux Administration
- Wazuh Deployment
- SIEM Administration
- Endpoint Monitoring
- Security Monitoring
- Agent Management
- Incident Detection
- GitHub Documentation
