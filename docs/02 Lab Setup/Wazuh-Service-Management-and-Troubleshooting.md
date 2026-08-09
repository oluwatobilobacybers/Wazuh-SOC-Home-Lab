# Wazuh Service Management and Troubleshooting

## Objective

This document records the procedures used to monitor, start, restart, and troubleshoot the core Wazuh services during the SOC Home Lab deployment.

The Wazuh platform consists of several interconnected services. Proper operation of the Wazuh Manager, Wazuh Indexer, and Wazuh Dashboard is required for successful security monitoring and alert visualization.

---

# Wazuh Architecture

The lab uses the following Wazuh components:

| Component | Function |
|-----------|----------|
| Wazuh Manager | Receives and analyzes security events from agents |
| Wazuh Indexer | Stores and indexes security event data |
| Wazuh Dashboard | Provides the web-based interface for security monitoring |

The Wazuh Manager receives events from the Ubuntu endpoint, processes them using decoders and rules, and forwards relevant data for storage and visualization.

---

# 1. Checking Wazuh Manager

The Wazuh Manager service was checked using:

```bash
sudo systemctl status wazuh-manager

A healthy service should show:
Active: active (running)

The service can also be checked using:
sudo systemctl is-active wazuh-manager

Expected result:
active

## Starting Wazuh Manager

If the Wazuh Manager is stopped:
sudo systemctl start wazuh-manager

Verify:
sudo systemctl status wazuh-manager

## Restarting Wazuh Manager

The Manager can be restarted after configuration changes or troubleshooting:
sudo systemctl restart wazuh-manager

Verify:
sudo systemctl status wazuh-manager

## Checking Wazuh Manager Logs

Manager logs were monitored using:
sudo tail -f /var/ossec/logs/ossec.log

Recent entries can be reviewed with:
sudo tail -100 /var/ossec/logs/ossec.log
```

# 2. Checking Wazuh Indexer

The Wazuh Indexer service was checked using:

```
sudo systemctl status wazuh-indexer

A healthy service should display:
Active: active (running)

The service state can also be checked with:
sudo systemctl is-active wazuh-indexer

Expected:
active

## Starting Wazuh Indexer

If the Indexer is not running:
sudo systemctl start wazuh-indexer

Verify:
sudo systemctl status wazuh-indexer

## Restarting Wazuh Indexer

The Indexer can be restarted when troubleshooting storage, indexing, or service-related problems:
sudo systemctl restart wazuh-indexer

Verify:
sudo systemctl status wazuh-indexer

## Checking Wazuh Indexer Logs
Indexer logs were reviewed when investigating service problems.

Example:
sudo journalctl -u wazuh-indexer -n 100 --no-pager

For live monitoring:
sudo journalctl -u wazuh-indexer -f
```

# 3. Checking Wazuh Dashboard

The Wazuh Dashboard service was checked using:

```
sudo systemctl status wazuh-dashboard

A healthy Dashboard should display:
Active: active (running)

Check service state:
sudo systemctl is-active wazuh-dashboard

Expected:
active

## Starting Wazuh Dashboard

If the Dashboard is stopped:
sudo systemctl start wazuh-dashboard

Verify:
sudo systemctl status wazuh-dashboard

## Restarting Wazuh Dashboard

The Dashboard can be restarted when troubleshooting web-interface or configuration problems:
sudo systemctl restart wazuh-dashboard

Verify:
sudo systemctl status wazuh-dashboard

## Checking Dashboard Logs

Dashboard logs were reviewed using:
sudo journalctl -u wazuh-dashboard -n 100 --no-pager

For live monitoring:
sudo journalctl -u wazuh-dashboard -f

# Checking All Wazuh Services

The three primary services can be checked together:

sudo systemctl status wazuh-manager wazuh-indexer wazuh-dashboard

A shorter check can be performed with:
sudo systemctl is-active wazuh-manager wazuh-indexer wazuh-dashboard

Expected Result:
active
active
active
```

# Service Startup Order

Ubuntu Agent
172.20.10.5
      |
      | TCP 1514
      v
Kali Linux / Wazuh Server
172.20.10.4
      |
      +--> Wazuh Manager
      |
      +--> Wazuh Indexer
      |
      +--> Wazuh Dashboard
      
The Manager handles security events, while the Indexer stores event data and the Dashboard provides visualization.

If the Dashboard is unavailable, the Indexer and Manager should therefore be checked before assuming the problem is with the web interface.

# Troubleshooting Workflow

When the Wazuh platform was unavailable, the following troubleshooting sequence was used:

Step 1 — Check Manager

sudo systemctl status wazuh-manager

Step 2 — Check Indexer

sudo systemctl status wazuh-indexer

Step 3 — Check Dashboard

sudo systemctl status wazuh-dashboard

Step 4 — Review logs

sudo journalctl -u wazuh-manager -n 50 --no-pager
sudo journalctl -u wazuh-indexer -n 50 --no-pager
sudo journalctl -u wazuh-dashboard -n 50 --no-pager

Step 5 — Restart the affected service

For Example:
sudo systemctl restart wazuh-manager

Step 6 — Verify service recovery

sudo systemctl is-active wazuh-manager

# Verifying Wazuh Manager Connectivity

The Wazuh Manager was also verified from the command line using:
sudo /var/ossec/bin/agent_control -lc

The lab successfully displayed the connected agents.

Example:
ID: 000, Name: kali (server), IP: 127.0.0.1, Active/Local
ID: 004, Name: ubuntu-agent, IP: any, Active

This confirmed that the Ubuntu endpoint had successfully enrolled and was communicating with the Wazuh Manager.

# Verifying Manager Network Connectivity

The Ubuntu agent was configured to communicate with the Wazuh Manager using:
Manager IP: 172.20.10.4
Protocol: TCP
Port: 1514

The agent configuration was verified with:
sudo grep "<client>" -A10 /var/ossec/etc/ossec.conf

The configuration showed:
<client>
  <server>
    <address>172.20.10.4</address>
    <port>1514</port>
    <protocol>tcp</protocol>
  </server>
</client>

The Ubuntu agent logs also confirmed successful communication:
Connected to the server ([172.20.10.4]:1514/tcp).

# Lessons Learned

Service-level troubleshooting was an important part of the Wazuh deployment.

The lab demonstrated that successful SIEM deployment requires more than installing the software. Each component must be monitored and verified independently.

Key lessons included:
- Always verify service status before troubleshooting configuration.
- Use systemd to manage Wazuh services.
- Review service logs when a component fails.
- Verify Manager-Agent connectivity separately from Dashboard availability.
- Restart services after configuration changes when required.
- Confirm recovery after every troubleshooting action.
- Treat the Wazuh Manager, Indexer, and Dashboard as interconnected components.

# Outcome

The Wazuh Manager, Indexer, and Dashboard services were successfully monitored and managed throughout the lab deployment and troubleshooting process.

The service-management procedures documented here became part of the operational workflow used while configuring the Ubuntu endpoint, validating agent connectivity, troubleshooting Wazuh communication, and preparing the lab for File Integrity Monitoring and subsequent attack simulations.
