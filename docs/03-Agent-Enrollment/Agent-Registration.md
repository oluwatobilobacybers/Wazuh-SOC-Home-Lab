# Wazuh Agent Registration and Enrollment

## Objective

This document records the process used to register and enroll the Ubuntu Server endpoint with the Wazuh Manager running on Kali Linux.

The objective was to establish authenticated and reliable communication between the Ubuntu endpoint and the Wazuh Manager so that endpoint telemetry, security events, system inventory, File Integrity Monitoring (FIM), and Security Configuration Assessment (SCA) data could be collected and analyzed through the Wazuh SOC infrastructure.

---

# Lab Environment

| Component | IP Address | Role |
|-----------|------------|------|
| Kali Linux | `172.20.10.4` | Wazuh Manager / Dashboard / Indexer |
| Ubuntu Server | `172.20.10.5` | Wazuh Agent / Monitored Endpoint |
| Metasploitable 2 | `172.20.10.2` | Deliberately Vulnerable Target |

### Manager-Agent Communication

```text
Ubuntu Server
172.20.10.5
Wazuh Agent
      |
      | TCP 1514
      v
Kali Linux
172.20.10.4
Wazuh Manager


# 1. Verify Wazuh Agent Installation

The Wazuh Agent was installed on the Ubuntu Server.

The agent service was verified using:
sudo systemctl status wazuh-agent

A healthy service should report:
Active: active (running)

The agent was also configured to start automatically after system reboot:
sudo systemctl enable wazuh-agent

# 2. Configure Wazuh Manager Address

The Wazuh Agent configuration file is located at:
/var/ossec/etc/ossec.conf

The Manager configuration was reviewed using:
sudo grep "<client>" -A10 /var/ossec/etc/ossec.conf

The relevant configuration was:
<client>
  <server>
    <address>172.20.10.4</address>
    <port>1514</port>
    <protocol>tcp</protocol>
  </server>
</client>

This configured the Ubuntu endpoint to communicate with the Wazuh Manager at.

Manager IP: 172.20.10.4
Protocol: TCP
Port: 1514

# 3. Agent Authentication

The Ubuntu endpoint was enrolled with the Wazuh Manager using the Wazuh authentication mechanism.

During the authentication process, the Ubuntu Agent requested an authentication key from the Wazuh Manager.

The agent authentication process was monitored through the Wazuh Agent logs.

Relevant messages included:
agent-auth: INFO: Requesting a key from server: 172.20.10.4
agent-auth: INFO: Using agent name as: ubuntu-agent
agent-auth: INFO: Waiting for server reply
agent-auth: INFO: Valid key received

The message:
Valid key received

confirmed that the Wazuh Manager successfully authenticated the Ubuntu endpoint.

# 4. Restart the Wazuh Agent

After authentication, the Wazuh Agent service was restarted to ensure the updated authentication configuration was loaded:
sudo systemctl restart wazuh-agent

The service was then verified:
sudo systemctl status wazuh-agent

The agent subsequently attempted to establish communication with the Wazuh Manager.

# 5. Verify Agent-to-Manager Connectivity

The Wazuh Agent logs were monitored to verify communication with the Manager:
sudo tail -f /var/ossec/logs/ossec.log

The logs showed the connection attempt:
Trying to connect to server ([172.20.10.4]:1514/tcp).

Successful communication was confirmed with:
Connected to the server ([172.20.10.4]:1514/tcp).

This confirmed that the Ubuntu endpoint could reach the Wazuh Manager over TCP port 1514.

# 6. Verify Agent Logs

The most recent Wazuh Agent log entries were reviewed using:
sudo tail -100 /var/ossec/logs/ossec.log

The logs were used to verify:
- Agent startup
- Authentication key loading
- Manager connection attempts
- Successful Manager connectivity
- Encryption configuration
- Syscheck startup
- Syscollector startup
- Security Configuration Assessment (SCA) activity

An example of successful communication was:
wazuh-agentd: INFO: Using AES as encryption method.
wazuh-agentd: INFO: Trying to connect to server ([172.20.10.4]:1514/tcp).
wazuh-agentd: INFO: (4102): Connected to the server ([172.20.10.4]:1514/tcp).

The presence of the AES encryption message confirmed that the configured agent communication was using the AES encryption method.

# 7. Verify Agent from the Wazuh Manager

The Wazuh Manager was checked from the Kali Linux server using:
sudo /var/ossec/bin/agent_control -lc

The output confirmed the presence of the Ubuntu endpoint:
Wazuh agent_control. List of available agents:
   ID: 000, Name: kali (server), IP: 127.0.0.1, Active/Local
   ID: 004, Name: ubuntu-agent, IP: any, Active

The following entry was particularly important:
ID: 004, Name: ubuntu-agent, IP: any, Active

This confirmed that:

1. The Ubuntu endpoint was registered with the Wazuh Manager.
2. The endpoint had been assigned Agent ID 004.
3. The Manager recognized the endpoint.
4. The endpoint was currently communicating with the Manager.


# 8. Validate the Complete Communication Path

The complete communication path was validated from both sides.

Ubuntu Server
172.20.10.5
      |
      | TCP/1514
      v
Wazuh Manager
172.20.10.4
      |
      v
Agent ID: 004
Name: ubuntu-agent
Status: Active

This established the communication foundation required for centralized security monitoring.


# 9. Troubleshooting During Enrollment

During the deployment process, the Wazuh Agent experienced temporary communication interruptions.

The Agent logs were monitored in real time:
sudo tail -f /var/ossec/logs/ossec.log

At one point, the logs reported:
wazuh-agentd: WARNING: Server unavailable. Setting lock.

The Agent then closed the existing connection:
wazuh-agentd: INFO: Closing connection to server ([172.20.10.4]:1514/tcp).

The Agent automatically attempted to reconnect:
wazuh-agentd: INFO: Trying to connect to server ([172.20.10.4]:1514/tcp).

The connection was subsequently restored:
wazuh-agentd: INFO: (4102): Connected to the server ([172.20.10.4]:1514/tcp).

The Agent then reported:
wazuh-agentd: INFO: Server responded. Releasing lock.

Troubleshooting Interpretation

The sequence demonstrated an important operational behavior:

Server unavailable
       ↓
Connection closed
       ↓
Automatic reconnection attempt
       ↓
Manager reachable
       ↓
Connection restored
       ↓
Agent resumes normal operation

Rather than immediately reinstalling the Agent, the logs were used to determine the actual communication state and confirm whether the service could recover successfully.

This reinforced the importance of reviewing logs before making unnecessary configuration changes.

# 10. Agent Enrollment Verification Checklist

| Verification                         | Status |
| ------------------------------------ | ------ |
| Wazuh Agent installed                | ✅      |
| Agent service running                | ✅      |
| Manager IP configured                | ✅      |
| TCP 1514 configured                  | ✅      |
| Agent authentication initiated       | ✅      |
| Authentication key received          | ✅      |
| Agent restarted after authentication | ✅      |
| Agent connected to Manager           | ✅      |
| Agent visible from Manager           | ✅      |
| Agent assigned ID `004`              | ✅      |
| Agent reported as Active             | ✅      |
| Agent logs confirmed communication   | ✅      |

# Outcome

The Ubuntu Server was successfully registered and enrolled as a Wazuh Agent and established communication with the Wazuh Manager running on Kali Linux.

The successful enrollment established the foundation for centralized endpoint monitoring, including:

- File Integrity Monitoring
- Security Configuration Assessment
- System inventory collection
- Log collection
- Security event detection
- Future attack-simulation monitoring

# Lessons Learned

The enrollment process demonstrated that installing a Wazuh Agent alone does not establish effective endpoint monitoring.

Successful integration requires several independent components to work correctly:

- Correct Manager IP address
- Correct communication port
- Network connectivity
- Successful agent authentication
- Valid authentication keys
- Running Agent service
- Successful Manager-Agent communication
- Manager-side agent verification
- Agent-side log verification

A particularly important troubleshooting lesson was the need to verify the communication state from both sides.

The Ubuntu Agent logs showed the endpoint's connection state, while the Wazuh Manager confirmed whether the endpoint was registered and active.

This two-sided validation provided a reliable way to distinguish between:
- Installation problems
- Authentication problems
- Configuration problems
- Network connectivity problems
- Temporary service interruptions
