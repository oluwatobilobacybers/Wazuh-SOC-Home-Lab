# Wazuh Agent Enrollment Troubleshooting

## Objective

This document records the troubleshooting activities performed during the enrollment and connectivity configuration of the Ubuntu Server Wazuh Agent with the Wazuh Manager running on Kali Linux.

The purpose is to document the diagnostic process used to identify authentication, service, configuration, and Manager-Agent communication issues encountered during the Wazuh SOC Home Lab deployment.

Rather than documenting only the successful configuration, this report captures the problems encountered, investigation steps, corrective actions, and validation procedures used to restore normal operation.

---

# Lab Environment

| Component | IP Address | Role |
|-----------|------------|------|
| Kali Linux | `172.20.10.4` | Wazuh Manager / Indexer / Dashboard |
| Ubuntu Server | `172.20.10.5` | Wazuh Agent / Monitored Endpoint |
| Metasploitable 2 | `172.20.10.2` | Deliberately Vulnerable Target |

### Communication Path

```text
Ubuntu Server
172.20.10.5
      |
      | TCP 1514
      v
Kali Linux
172.20.10.4
Wazuh Manager

# Troubleshooting Methodology

The troubleshooting process followed a layered approach rather than immediately reinstalling or modifying the Wazuh Agent.

1. Verify network connectivity
          ↓
2. Verify Wazuh Manager service
          ↓
3. Verify Agent service
          ↓
4. Verify Agent configuration
          ↓
5. Verify authentication
          ↓
6. Review Agent logs
          ↓
7. Review Manager logs/status
          ↓
8. Restart affected service
          ↓
9. Re-test connectivity
          ↓
10. Confirm Agent status from Manager

This approach helped distinguish between network, service, configuration, and authentication problems.

# 1. Verify Ubuntu Network Connectivity

The first step when investigating Agent communication was to confirm that the Ubuntu endpoint could reach the Wazuh Manager.

From Ubuntu:
ping -c 4 172.20.10.4

The Manager IP address was confirmed as:
172.20.10.4

The Ubuntu endpoint was:
172.20.10.5

Successful ICMP responses provided an initial indication that network connectivity existed between the two systems.

# 2. Verify Wazuh Manager Service

Because the Agent depends on the Wazuh Manager for authentication and event communication, the Manager service was checked from Kali Linux.
sudo systemctl status wazuh-manager

The service was expected to report:
Active: active (running)

If necessary, the service could be restarted:
sudo systemctl restart wazuh-manager

The service was then validated again:
sudo systemctl is-active wazuh-manager

Expected:
active

# 3. Verify Wazuh Agent Service

The Agent service was checked from Ubuntu:
sudo systemctl status wazuh-agent

If the service was not running:
sudo systemctl start wazuh-agent

or:
sudo systemctl restart wazuh-agent

The final state was confirmed with:
sudo systemctl is-active wazuh-agent

Expected:
active

# 4. Verify Agent Configuration

The Agent configuration file was reviewed:
sudo grep "<client>" -A10 /var/ossec/etc/ossec.conf

The Manager configuration was expected to contain:
<client>
  <server>
    <address>172.20.10.4</address>
    <port>1514</port>
    <protocol>tcp</protocol>
  </server>
</client>

This confirmed that the Agent was configured to communicate with the correct Wazuh Manager using TCP port 1514.

# 5. Verify Wazuh Manager Listening Port

The Manager's listening sockets were checked from Kali Linux:
sudo ss -tulpn | grep 1514

This was used to determine whether the Wazuh Manager was listening for Agent connections.

The expected communication path was:

Ubuntu Agent
172.20.10.5
      |
      | TCP 1514
      v
Kali Wazuh Manager
172.20.10.4

# 6. Investigate Agent Authentication

Agent authentication was investigated when enrollment did not immediately result in an active connection.

The Agent authentication process was monitored through:
sudo tail -f /var/ossec/logs/ossec.log

Relevant authentication messages included:
Requesting a key from server: 172.20.10.4
Using agent name as: ubuntu-agent
Waiting for server reply
Valid key received

The presence of:
Valid key received

confirmed that the Manager had successfully authenticated the endpoint and issued the required authentication key.

# 7. Investigate Agent Connectivity

After authentication, the Agent logs were monitored for connection attempts:
sudo tail -f /var/ossec/logs/ossec.log

The Agent reported:
Trying to connect to server ([172.20.10.4]:1514/tcp).

Successful communication was confirmed by:
Connected to the server ([172.20.10.4]:1514/tcp).

This demonstrated that authentication and network communication were functioning.

# 8. Temporary Manager Unavailability

During the deployment and troubleshooting process, the Agent logs also recorded a temporary communication interruption.

The Agent reported:
wazuh-agentd: WARNING: Server unavailable. Setting lock.

The connection was then closed:
wazuh-agentd: INFO: Closing connection to server ([172.20.10.4]:1514/tcp).

The Agent automatically attempted to reconnect:
wazuh-agentd: INFO: Trying to connect to server ([172.20.10.4]:1514/tcp).

Communication was subsequently restored:
wazuh-agentd: INFO: (4102): Connected to the server ([172.20.10.4]:1514/tcp).

The Agent then reported:
wazuh-agentd: INFO: Server responded. Releasing lock.

Interpretation

The sequence demonstrated that the Agent was capable of detecting the Manager's temporary unavailability and automatically attempting to restore communication.

This was important because it showed that a temporary communication warning did not necessarily require Agent reinstallation or re-enrollment.

# 9. Verify Agent Status from the Manager

After troubleshooting, the Manager was used to verify the final Agent state:
sudo /var/ossec/bin/agent_control -lc

The Ubuntu endpoint was displayed as:
ID: 004, Name: ubuntu-agent, IP: any, Active

This provided Manager-side confirmation that the endpoint was successfully connected.

# 10. Verify Agent Logs After Recovery

The Agent logs were reviewed again after connectivity was restored:
sudo tail -100 /var/ossec/logs/ossec.log

The recovery sequence was verified by looking for successful communication messages such as:
Connected to the server ([172.20.10.4]:1514/tcp).

This prevented the troubleshooting process from ending immediately after restarting a service.

The system was considered recovered only after communication was independently verified.

11. Troubleshooting Decision Matrix

| Symptom                   | Investigation                    | Corrective Action                   |
| ------------------------- | -------------------------------- | ----------------------------------- |
| Agent service inactive    | `systemctl status wazuh-agent`   | Start/restart Agent                 |
| Manager inactive          | `systemctl status wazuh-manager` | Start/restart Manager               |
| Server unavailable        | Review Agent logs                | Check Manager/service/network       |
| Authentication failure    | Review authentication logs       | Verify enrollment/configuration     |
| Wrong Manager address     | Review `ossec.conf`              | Correct Manager IP                  |
| Wrong communication port  | Review `ossec.conf`              | Confirm TCP 1514                    |
| Agent not visible         | `agent_control -lc`              | Verify enrollment/connectivity      |
| Temporary connection loss | Monitor `ossec.log`              | Allow/retest automatic reconnection |
| Dashboard unavailable     | Check Manager/Indexer/Dashboard  | Troubleshoot service dependencies   |

12. Validation Checklist

| Validation                                 | Result         |
| ------------------------------------------ | -------------- |
| Ubuntu network connectivity                | ✅ Verified     |
| Wazuh Manager running                      | ✅ Verified     |
| Wazuh Agent running                        | ✅ Verified     |
| Manager IP configured correctly            | ✅ Verified     |
| TCP 1514 configured                        | ✅ Verified     |
| Manager listening state checked            | ✅ Verified     |
| Authentication key received                | ✅ Verified     |
| Agent connection established               | ✅ Verified     |
| Temporary connection interruption observed | ✅ Investigated |
| Automatic reconnection observed            | ✅ Verified     |
| Agent visible from Manager                 | ✅ Verified     |
| Agent status Active                        | ✅ Verified     |
| Agent logs reviewed after recovery         | ✅ Verified     |

# Lessons Learned

The troubleshooting process reinforced several important SOC and security engineering practices.

## 1. Check before changing

Service status, configuration, connectivity, and logs should be checked before making changes.

## 2. Logs provide operational evidence

The Wazuh Agent logs were essential for distinguishing between:

- Authentication problems
- Configuration problems
- Network problems
- Manager availability problems
- Temporary communication interruptions

## 3. Validate from both sides

A successful Agent connection should be verified from both:

- The Ubuntu Agent
- The Wazuh Manager

## 4. Do not immediately reinstall

Temporary communication failures can recover automatically.

Understanding the failure state first prevents unnecessary reinstallation and configuration changes.

## 5. Validate recovery

A restart is not proof that a problem has been solved.

The service and communication path must be checked again after corrective action.

# Outcome

The Wazuh Agent enrollment and connectivity issues encountered during the deployment were successfully investigated using a structured troubleshooting methodology.

The final validation confirmed:

Ubuntu Agent
172.20.10.5
      |
      | TCP 1514
      |
      v
Wazuh Manager
172.20.10.4
      |
      v
Agent ID 004
ubuntu-agent
ACTIVE

The troubleshooting process established a reliable foundation for the next phase of the project: endpoint telemetry validation and File Integrity Monitoring.
