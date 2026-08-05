# Oracle VirtualBox 7.2.6 Installation and Lab Configuration

## Objective

This document describes the installation and configuration of Oracle VirtualBox 7.2.6, which serves as the virtualization platform for the Wazuh SOC Home Lab.

The hypervisor provides an isolated environment for deploying multiple virtual machines required for building a Security Operations Center (SOC) laboratory without impacting the host operating system.

---

# Environment

| Component | Details |
|----------|---------|
| Host Operating System | Windows 11 Pro (64-bit) |
| Hypervisor | Oracle VirtualBox 7.2.6 |
| Host RAM | 16 GB RAM (16384) |
| Host CPU | 11th Gen Intel® Core™ i5-1145G7 @ 2.60 GHz |
| Virtual Network | Internal Network |

---

# Purpose

Oracle VirtualBox was selected to provide a lightweight virtualization platform capable of hosting multiple operating systems simultaneously.

The virtual machines deployed in this lab include:

- Kali Linux (SOC Analyst Workstation and Wazuh SIEM Platform)
- Ubuntu Server 24.04 LTS (Wazuh Agent)
- Metasploitable 2 (Vulnerable Machine)

This configuration simulates a small enterprise network where a SIEM monitors endpoints for security events.

---

# Lab Topology

```
Host Computer (Windows 11)
│
├── Oracle VirtualBox 7.2.6
│
├── Kali Linux
│      ├── Wazuh Manager
│      ├── Wazuh Dashboard
│      └── Wazuh Indexer
│
├── Ubuntu Server
│      └── Wazuh Agent
│
└── Metasploitable2
       └── Vulnerable Target
```

---

# Installation Procedure

## Step 1 — Download Oracle VirtualBox

Download Oracle VirtualBox from the official website:

[Oracle VirtualBox Downloads](https://www.virtualbox.org/wiki/Downloads)

---

## Step 2 — Install Oracle VirtualBox

Launch the installer and accept the default installation options.

The following components were installed:

- VirtualBox Application
- USB Support
- Networking Components
- Python Support

---

## Step 3 — Install VirtualBox Extension Pack

After installing VirtualBox, download and install the matching Oracle VM VirtualBox Extension Pack.

The Extension Pack enables:

- USB 2.0 / USB 3.0 Support
- VirtualBox Remote Desktop (VRDP)
- Improved device compatibility
- Additional virtualization features

---

## Step 4 — Verify Installation

Launch Oracle VirtualBox.

Successful installation is confirmed when the VirtualBox Manager opens without errors.

---

# Initial VirtualBox Configuration

The following settings were configured before creating virtual machines.

## Default Machine Folder

Configure the default VM storage location according to available disk capacity.

Example:

```
D:\Virtual Machines\
```

---

## Network Configuration

A dedicated Internal Network was created to isolate the SOC laboratory from the production network.

Example network name:

```
SOC-LAB
```

This allows communication only between the virtual machines participating in the lab.

---

## Resource Allocation

Resources were allocated according to the host system capacity.

Typical allocations:

| Virtual Machine | RAM | CPU |
|----------------|-----|-----|
| Kali Linux | 6 GB | 2 vCPU |
| Ubuntu Server | 4 GB | 2 vCPU |
| Metasploitable2 | 2 GB | 1 vCPU |

---

# Verification

The installation was verified by successfully launching Oracle VirtualBox and creating virtual machines for:

- Kali Linux
- Ubuntu Server
- Metasploitable2

Each virtual machine booted successfully and was accessible through the VirtualBox Manager.

---

# Challenges Encountered

During the initial setup, no major installation issues were encountered.

Subsequent challenges related to networking, SSH connectivity, and Wazuh deployment are documented in later sections of this repository.

---

# Lessons Learned

- Oracle VirtualBox provides an effective platform for building isolated cybersecurity laboratories.
- Proper resource allocation is important for maintaining system performance.
- Using an isolated virtual network reduces the risk of unintended interaction with the host or external systems.
- Establishing the virtualization environment correctly simplifies later stages of SIEM deployment and endpoint integration.

---

# References

Oracle VM VirtualBox Documentation

https://www.virtualbox.org/manual/

Oracle VM VirtualBox Downloads

https://www.virtualbox.org/wiki/Downloads
