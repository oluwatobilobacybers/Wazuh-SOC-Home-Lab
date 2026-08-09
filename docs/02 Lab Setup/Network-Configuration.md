# Network Configuration

## Objective

This document details the network configuration used for the Wazuh SOC Home Lab. The objective was to establish reliable communication between the SOC analyst workstation, Wazuh server, monitored Ubuntu endpoint, and the vulnerable Metasploitable 2 machine within the isolated laboratory environment.

The network configuration was designed to allow security monitoring, agent communication, SSH administration, and controlled attack simulation without exposing the vulnerable systems unnecessarily to the external network.

---

## Lab Network Overview

The laboratory consists of multiple virtual machines operating within the same lab network.

| Device | IP Address | Role |
|---|---|---|
| Kali Linux | 172.20.10.4 | Wazuh Server / SOC Analyst Workstation |
| Ubuntu Server | 172.20.10.5 | Wazuh Agent / Monitored Endpoint |
| Metasploitable 2 | 172.20.10.2 | Vulnerable Target |

> **Note:** IP addresses may change depending on the virtual network configuration and DHCP assignments. The addresses documented above represent the configuration used during the current phase of the lab.

---

# Virtualization Platform

The virtual machines were deployed using:

**Oracle VirtualBox 7.2.6**

The virtual machines were configured to communicate through a controlled virtual network.

The purpose of using a dedicated laboratory network was to:

- Allow communication between lab machines
- Enable Wazuh agent-to-manager communication
- Allow SSH administration
- Support vulnerability assessment and attack simulation
- Prevent unnecessary exposure of vulnerable systems
- Provide a realistic environment for SOC monitoring

---

# Network Roles

### Kali Linux — 172.20.10.4

Kali Linux serves as the primary SOC environment and Wazuh server.

Its responsibilities include:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- SOC analyst workstation
- Security investigation
- Network reconnaissance
- Vulnerability assessment
- Log and alert analysis

---

### Ubuntu Server — 172.20.10.5

Ubuntu Server operates as a monitored endpoint running the Wazuh agent.

The endpoint is used to generate security telemetry that can be collected and analyzed by the Wazuh Manager.

Monitoring includes:

- File Integrity Monitoring
- System inventory
- Security Configuration Assessment
- System logs
- Package information
- Running processes
- Network information

---

### Metasploitable 2 — 172.20.10.2

Metasploitable 2 will serve as the intentionally vulnerable victim machine.

It will be introduced during the attack-simulation phase of the project.

Its purpose is to provide a controlled target for:

- Network reconnaissance
- Vulnerability assessment
- Attack simulation
- Detection engineering
- Incident investigation
- MITRE ATT&CK mapping

No attacks are performed against systems outside the controlled laboratory environment.

---

# Connectivity Verification

Connectivity between the virtual machines was verified using `ping`.

From Kali Linux:

```bash
ping -c 4 172.20.10.5
