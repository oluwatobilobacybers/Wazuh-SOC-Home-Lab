# Wazuh SOC Home Lab

A hands-on Security Operations Center (SOC) home lab designed to simulate a small enterprise security monitoring environment using **Wazuh SIEM**, **Kali Linux**, **Ubuntu Server**, and **Metasploitable 2**.

The project focuses on practical security operations, including SIEM deployment, endpoint monitoring, agent enrollment, log analysis, File Integrity Monitoring (FIM), security configuration assessment, vulnerability assessment, alert investigation, and controlled attack simulation.

> 🚧 **Project Status: Active Development**



# Project Overview

This project was created to build practical, portfolio-driven SOC experience by deploying and operating a Wazuh-based security monitoring environment from the ground up.

The lab is designed to simulate the workflow of a SOC analyst:

**Deploy → Configure → Monitor → Detect → Investigate → Respond → Document**

Every major configuration, troubleshooting process, security event, and investigation is being documented to demonstrate practical cybersecurity capability rather than certification knowledge alone.



# Lab Objectives

The primary objectives of the lab are to:

- Deploy and configure Wazuh SIEM.
- Configure the Wazuh Manager, Indexer, and Dashboard.
- Deploy and enroll a monitored Ubuntu endpoint.
- Establish secure Manager-Agent communication.
- Monitor endpoint activity and system changes.
- Configure and validate File Integrity Monitoring (FIM).
- Perform Security Configuration Assessment (SCA).
- Collect and analyze security logs.
- Generate controlled security events.
- Investigate alerts from a SOC analyst perspective.
- Perform vulnerability assessment against a deliberately vulnerable system.
- Develop custom detection rules.
- Practice incident investigation and response.
- Document the complete SOC workflow using Git and GitHub.



# Lab Environment

| Component | Specification |
|-----------|---------------|
| Hypervisor | Oracle VirtualBox 7.2.6 |
| Host OS | Windows 11 Pro |
| Wazuh Server | Kali Linux |
| Wazuh Server IP | 172.20.10.4 |
| Monitored Endpoint | Ubuntu Server 24.04 LTS |
| Ubuntu Agent IP | 172.20.10.5 |
| Vulnerable Target | Metasploitable 2 |
| Metasploitable2 IP | 172.20.10.2 |
| SIEM Platform | Wazuh 4.14.x |
| Network | Isolated Virtual Lab Network |



# Lab Architecture

The current lab consists of three primary virtual machines:


                      SOC ANALYST WORKSTATION
                           Windows 11 Host
                                |
                                |
                         VirtualBox 7.2.6
                                |
                    Isolated Virtual Network
                         172.20.10.0/24
                                |
              +-----------------+------------------+
              |                                    |
       +------+-------+                     +------+-------+
       |  Kali Linux  |                     |    Ubuntu    |
       | 172.20.10.4  |                     | 172.20.10.5 |
       |              |                     |              |
       | Wazuh        |<---- TCP 1514 ---->| Wazuh Agent  |
       | Manager      |                     |              |
       | Indexer      |                     | FIM          |
       | Dashboard    |                     | SCA          |
       |              |                     | Syscollector  |
       +------+-------+                     +--------------+
              |
              |
              | Security Monitoring
              | / Vulnerability Assessment
              |
       +------+----------------+
       |  Metasploitable 2     |
       |     172.20.10.2       |
       |                       |
       | Deliberately          |
       | Vulnerable Target     |
       +-----------------------+



                    WAZUH SOC HOME LAB
                           │
                           ▼
                 ┌───────────────────┐
                 │ 01 Lab Overview   │
                 │       ✅          │
                 └─────────┬─────────┘
                           ▼
                 ┌───────────────────┐
                 │ 02 Lab Setup      │
                 │       ✅          │
                 └─────────┬─────────┘
                           ▼
                 ┌───────────────────┐
                 │ 03 Agent          │
                 │    Enrollment✅  │
                 └─────────┬─────────┘
                           ▼
                ┌───────────────────┐
                │ 04 Detection Lab  │
                │                   │
                │ FIM          ✅   │
                │ SCA          ✅   │
                │ Log Analysis ✅  │
                └─────────┬─────────┘
                          ▼
                ┌───────────────────┐
                │ 05 Attack         │
                │    Simulation     │
                │       🔄          │
                └─────────┬─────────┘
                          ▼
                ┌───────────────────┐
                │ 06 Incident       │
                │    Response       │
                │       🔄          │
                └─────────┬─────────┘
                          ▼
                ┌───────────────────┐
                │ 07 Custom Rules   │
                │       🔜          │
                └─────────┬─────────┘                           ▼
                 ┌───────────────────┐
                 │ 08 Dashboards     │
                 └─────────┬─────────┘
                           ▼
                 ┌───────────────────┐
                 │ 09 Network        │
                 │    Architecture   │
                 └───────────────────┘


# Network Roles

| System           | IP Address  | Role                                |
| ---------------- | ----------- | ----------------------------------- |
| Kali Linux       | 172.20.10.4 | Wazuh Server / SOC Analyst Platform |
| Ubuntu Server    | 172.20.10.5 | Monitored Wazuh Endpoint            |
| Metasploitable 2 | 172.20.10.2 | Deliberately Vulnerable Target      |

The Metasploitable 2 system is intentionally vulnerable and will only be used within the controlled laboratory environment for security testing and detection exercises.


# Technologies & Security Capabilities

### Platforms & Infrastructure
- Oracle VirtualBox 7.2.6
- Windows 11 Pro
- Kali Linux
- Ubuntu Server 24.04 LTS
- Metasploitable 2


### Security Technologies
- Wazuh SIEM
- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- Wazuh Agent
- File Integrity Monitoring (FIM)
- Security Configuration Assessment (SCA)
- Syscollector

### Networking & Administration
- TCP/IP
- SSH
- Linux System Administration
- Network Troubleshooting
- Manager-Agent Communication

### Security Operations
- Security Monitoring
- Log Analysis
- Alert Investigation
- Vulnerability Assessment
- Incident Investigation
- Detection Engineering

### Documentation & Version Control
- Git
- GitHub
- Markdown



# Skills Demonstrated

- SIEM Deployment
- Security Monitoring
- Linux Administration
- Threat Detection
- Endpoint Monitoring
- File Integrity Monitoring
- Incident Investigation
- Alert Analysis
- Log Management
- Security Documentation
- Git Version Control

# Security Tools

- Nmap
- Wireshark
- Nessus
- Metasploit



# Lab Components

| Component        | Purpose                            |
| ---------------- | ---------------------------------- |
| Kali Linux       | Wazuh Manager, Indexer & Dashboard / SOC Platform |
| Ubuntu Server    | Monitored Wazuh Endpoint           |
| Metasploitable 2 | Deliberately vulnerable target    |
| Wazuh Agent      | Endpoint monitoring                |
| Wazuh Dashboard  | Security alert visualization       |



# Project Structure

```
Wazuh-SOC-Home-Lab/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── docs/
│   ├── 01-Lab-Overview/
│   │   ├── Lab-Objectives.md
│   │   ├── Architecture.md
│   │   └── Technologies.md
│   │
│   ├── 02-Lab-Setup/
│   │   ├── OracleVirtualBox7.2.6.md
│   │   ├── Copy-Paste-Between-Windows-and-Ubuntu.md
│   │   ├── Network-Configuration.md
│   │   ├── Wazuh-Installation.md
│   │   ├── Wazuh-Service-Management-and-Troubleshooting.md
│   │   └── screenshots/
│   │
│   ├── 03-Agent-Enrollment/
│   │   ├── Agent-Registration.md
│   │   ├── Troubleshooting.md
│   │   └── screenshots/
│   │
│   ├── 04-Detection-Lab/
│   │   ├── File-Integrity-Monitoring.md
│   │   └── screenshots/
│   │
│   ├── 05-Attack-Simulation/
│   ├── 06-Incident-Response/
│   ├── 07-Custom-Rules/
│   ├── 08-Dashboards/
│   └── 09-Network-Diagram/
│
├── Images/
└── 
```


### Detection & Investigation

- [File Integrity Monitoring](docs/04-Detection-Lab/File-Integrity-Monitoring.md)
- [Exercise 2 – SSH Brute-Force Detection & Firewall-drop Active Response](docs/04-Detection-Lab/Active-Response-Firewall-Drop.md)
- Exercise 3 – Endpoint Security Event Investigation — In Progress

# Documentation Philosophy

This project does not document only successful configurations.

Troubleshooting experiences are also documented because real-world SOC and security engineering work involves:

- Diagnosing service failures
- Reviewing logs
- Verifying network connectivity
- Investigating authentication problems
- Correcting configuration errors
- Validating recovery
- Understanding why a security control failed

The objective is therefore to demonstrate problem-solving ability, troubleshooting methodology, and operational understanding, not simply the ability to follow installation instructions.

# Exercise Progress

| Exercise | Topic | Status |
|----------|-------|--------|
| Exercise 1 | File Integrity Monitoring | ✅ Completed |
| Exercise 2 | SSH Brute-Force Detection & Firewall-drop Active Response | ✅ Completed* |
| Exercise 3 | Endpoint Security Event Investigation | 🚧 In Progress |

\* Firewall-drop containment, Rule 651 host blocking, delete operation, and final firewall state were verified. Direct evidence of Rule 652 – Host Unblocked by firewall-drop Active Response remains pending.


# Current Progress

- [x] VirtualBox laboratory environment configured
- [x] Kali Linux configured
- [x] Ubuntu Server 24.04 LTS deployed
- [x] Metasploitable 2 deployed
- [x] Wazuh Manager installed
- [x] Wazuh Indexer installed
- [x] Wazuh Dashboard installed
- [x] Wazuh services validated
- [x] Wazuh Agent installed on Ubuntu
- [x] Ubuntu Agent enrolled with Wazuh Manager
- [x] Manager-Agent connectivity verified
- [x] Syscollector enabled
- [x] Security Configuration Assessment (SCA) enabled
- [x] SSH administration from Windows PowerShell configured
- [x] Wazuh service management and troubleshooting documented
- [x] GitHub repository established

### Detection & Investigation

- [x] File Integrity Monitoring configured
- [x] FIM file creation detection validated
- [x] FIM file modification detection validated
- [x] FIM file deletion detection validated
- [x] Wazuh FIM alerts investigated
- [x] Metasploitable 2 network reconnaissance completed
- [x] vsftpd 2.3.4 service identified
- [x] CVE-2011-2523 vulnerability verified in the controlled lab
- [x] Vulnerability assessment documented
- [x] SSH authentication failure detection validated
- [x] Wazuh Rule 2502 detection investigated
- [x] Firewall-drop Active Response configured and triggered
- [x] Firewall-drop source IP containment validated
- [x] Wazuh Rule 651 host-blocking event verified
- [x] Firewall-drop delete operation verified
- [x] Final iptables and nftables state verified
- [ ] Wazuh Rule 652 unblocking alert requires direct evidence


# In Progress

- [ ] Exercise 3 – Endpoint Security Event Investigation
- [ ] Document Exercise 3 procedure and evidence
- [ ] Validate and investigate additional endpoint security events


# Planned

- [ ] MITRE ATT&CK mapping
- [ ] Custom Wazuh detection rules
- [ ] Suricata integration
- [ ] YARA integration
- [ ] Sigma rules
- [ ] Additional endpoint telemetry
- [ ] Sysmon integration
- [ ] Docker monitoring
- [ ] Kubernetes monitoring
- [ ] OpenShift monitoring

# Skills Demonstrated

- SIEM Deployment
- SOC Operations
- Security Monitoring
- Endpoint Monitoring
- Linux Administration
- Wazuh Administration
- Agent Enrollment
- Manager-Agent Communication
- File Integrity Monitoring
- Security Configuration Assessment
- Log Analysis
- Alert Investigation
- Network Troubleshooting
- Vulnerability Assessment
- Incident Investigation
- Security Documentation
- Git & GitHub


# Planned Improvements

- Sysmon Integration
- Suricata Integration
- YARA Integration
- Sigma Rules
- Active Response
- MITRE ATT&CK Mapping
- Vulnerability Detection
- Docker Monitoring
- Kubernetes Monitoring
- OpenShift Monitoring



# Lessons Learned

Throughout the project, several important lessons have emerged:

- Installing a SIEM is only the beginning of the deployment process.
- Every Wazuh component must be validated independently.
- Manager-Agent communication must be verified separately from Dashboard availability.
- Service logs are critical when troubleshooting Wazuh.
- Configuration changes should always be followed by validation.
- Security monitoring requires reliable endpoint telemetry.
- FIM requires both correct configuration and deliberate validation.
- Practical hands-on troubleshooting provides deeper understanding than theoretical study alone.
- Proper documentation is an important part of professional security operations.


## 📸 Project Screenshots

Screenshots documenting the lab will be stored under the appropriate documentation directories.

### Wazuh Dashboard

![Dashboard](docs/screenshots/dashboard-home.png)


### Connected Agents

![Agents](docs/screenshots/agents-active.png)


### File Integrity Monitoring

![FIM](docs/screenshots/fim-alert.png)



### Security Events

![Events](docs/screenshots/security-events.png)

# Documentation

Detailed implementation guides will be added under the **docs** directory.


## 📚 Documentation

### Lab Setup
- [Oracle VirtualBox 7.2.6](docs/02-Lab-Setup/OracleVirtualBox7.2.6.md)
- [Copy & Paste Between Windows and Ubuntu](docs/02-Lab-Setup/Copy-Paste-Between-Windows-and-Ubuntu.md)
- [Network Configuration](docs/02-Lab-Setup/Network-Configuration.md)
- [Wazuh Installation](docs/02-Lab-Setup/Wazuh-Installation.md)
- [Wazuh Service Management & Troubleshooting](docs/02-Lab-Setup/Wazuh-Service-Management-and-Troubleshooting.md)

### Agent Enrollment
- [Agent Registration](docs/03-Agent-Enrollment/Agent-Registration.md)
- [Agent Troubleshooting](docs/03-Agent-Enrollment/Troubleshooting.md)

### Detection & Investigation
- Detection Lab — In Progress
- Attack Simulation — In Progress
- Incident Response — In Progress
- Custom Detection Rules — In Progress


# Author

Banjo Oluwatobiloba Adekunle

Aspiring SOC Analyst | CompTIA Security+ | ISC² Certified in Cybersecurity (CC)

GitHub:
https://github.com/oluwatobilobacybers

LinkedIn:
https://linkedin.com/in/oluwatobiloba-banjo


# Disclaimer

This project was created strictly for educational and defensive cybersecurity purposes.

All vulnerability assessments, attack simulations, and exploitation activities are conducted within an isolated laboratory environment against intentionally vulnerable systems.
