# Wazuh SIEM Installation and Deployment

## Objective

This document records the deployment of the Wazuh SIEM platform used in the Wazuh SOC Home Lab.

The Wazuh environment was deployed on Kali Linux and consists of the core Wazuh components required for centralized security monitoring:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard

The deployment process also involved service validation and troubleshooting to ensure that the Wazuh platform was operational before connecting monitored endpoints.

---

# Environment

| Component | Specification |
|---|---|
| Operating System | Kali Linux |
| Wazuh Version | 4.14.x |
| Wazuh Manager | Installed |
| Wazuh Indexer | Installed |
| Wazuh Dashboard | Installed |
| Network Address | 172.20.10.4 |
| Virtualization | Oracle VirtualBox 7.2.6 |

---

# Wazuh Architecture

The Wazuh deployment uses three primary server-side components.

### Wazuh Manager

The Wazuh Manager is responsible for:

- Receiving agent data
- Analyzing security events
- Applying detection rules
- Generating alerts
- Managing agents
- Supporting active response

### Wazuh Indexer

The Wazuh Indexer stores and indexes security events and alerts, allowing them to be searched and analyzed efficiently.

### Wazuh Dashboard

The Wazuh Dashboard provides the graphical interface used by the SOC analyst to:

- Monitor security events
- Investigate alerts
- Review agents
- Analyze FIM events
- Review security configuration results
- Investigate endpoint activity

---

# Installation Process

The Wazuh platform was installed on the Kali Linux virtual machine.

The installation involved configuring the Wazuh repository, installing the required components, and starting the corresponding services.

The deployment was subsequently validated through service-status checks and Wazuh logs.

---

# Initial Service Validation

After installation, the status of the Wazuh Manager was checked using:

```bash
sudo systemctl status wazuh-manager
