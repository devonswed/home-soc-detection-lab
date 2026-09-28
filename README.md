# Home SOC Detection & Network Reconnaissance Lab

A virtualized home SOC environment built to simulate, detect, and investigate network reconnaissance against a Windows endpoint.

This project demonstrates an end-to-end security monitoring and detection workflow using **Wazuh, Splunk Enterprise, Sysmon, Windows Firewall logging, Wireshark, Nmap, and Sigma**. Controlled reconnaissance was generated from a Kali Linux attacker VM and analyzed across multiple telemetry sources and SIEM platforms.

## Project Objectives

- Build a functional SOC lab using virtual machines and freely available security tools.
- Generate controlled network reconnaissance against a monitored Windows endpoint.
- Collect and analyze endpoint and network telemetry.
- Develop custom detections in Wazuh and Splunk.
- Correlate SIEM alerts with packet-level evidence in Wireshark.
- Map detected activity to the MITRE ATT&CK framework.
- Create vendor-neutral Sigma detection rules and translate them into Splunk SPL.
- Validate detections against real telemetry generated inside the lab.
## Lab Architecture

The lab was built in VMware Workstation using three virtual machines connected through a VMware NAT network.
![Home SOC Lab Architecture](screenshots/architecture/SOC_Lab_Architecture.png)

| System | Role | IP Address |
|---|---|---|
| Kali Linux | Attacker / security testing system | 192.168.134.130 |
| Windows 11 | Monitored SOC endpoint | 192.168.134.129 |
| Ubuntu Server | Wazuh SIEM server | 192.168.134.128 |

### Security Stack

| Tool | Purpose |
|---|---|
| Wazuh | SIEM/XDR monitoring and custom detection |
| Splunk Enterprise | Log analysis, SPL detection, correlation, and alerting |
| Sysmon | Detailed Windows endpoint telemetry |
| Windows Firewall | Network connection and dropped-packet telemetry |
| Wireshark | Packet capture and network traffic validation |
| Nmap | Controlled network reconnaissance |
| Sigma | Vendor-neutral detection rules and correlation logic |

## Detection Scenario: Network Reconnaissance

### Scenario Overview

To validate the monitoring and detection capabilities of the lab, I performed a controlled network reconnaissance exercise from the Kali Linux attacker VM against the Windows 11 endpoint.

An Nmap TCP SYN scan was generated from the Kali system (`192.168.134.130`) targeting the Windows endpoint (`192.168.134.129`). The Windows Firewall blocked the inbound connection attempts and recorded the activity in its firewall log.

The resulting telemetry was analyzed across multiple layers of the lab:

- **Windows Firewall** recorded the blocked TCP connection attempts.
- **Wazuh** ingested the firewall telemetry and generated a custom reconnaissance detection.
- **Wireshark** provided packet-level validation of the scan traffic.
- **Splunk Enterprise** was used to search, extract, correlate, and analyze the firewall events.
- **Sigma** was used to create vendor-neutral detection logic and translate the detection into Splunk SPL.
- The activity was mapped to **MITRE ATT&CK T1046 – Network Service Discovery/Scanning**.

### 1. Reconnaissance Generation

Network reconnaissance was generated from the Kali Linux attacker VM using Nmap. A TCP SYN scan targeted the Windows 11 endpoint:

```bash
sudo nmap -Pn -sS 192.168.134.129
```

The scan probed the Windows endpoint for accessible TCP services. The Windows Firewall filtered the probes, resulting in Nmap reporting the scanned ports as filtered/no-response.

#### Nmap Scan Evidence

![Kali Linux Nmap SYN scan](screenshots/detection-scenario/01-kali-nmap-syn-scan.png)

*Figure 1 — Controlled TCP SYN reconnaissance performed from the Kali Linux attacker VM against the monitored Windows 11 endpoint. The target remained reachable while the Windows Firewall filtered the scanned TCP ports.*

### 2. Windows Firewall Telemetry

The Windows 11 endpoint was configured to log dropped network connections through Windows Defender Firewall. Following the Nmap SYN scan, the firewall log recorded repeated TCP connection attempts originating from the Kali Linux attacker VM (`192.168.134.130`) and targeting the monitored Windows endpoint (`192.168.134.129`).

The log entries show the firewall dropping TCP traffic across multiple destination ports, providing host-level evidence of the reconnaissance activity.

#### Firewall Log Evidence

![Windows Firewall dropped TCP traffic](screenshots/detection-scenario/02-windows-firewall-dropped-traffic.png)
*Figure 2 — Windows Firewall telemetry showing dropped TCP connection attempts from the Kali Linux attacker VM (`192.168.134.130`) to the monitored Windows 11 endpoint (`192.168.134.129`).*

### 3. Wazuh Detection

Windows Firewall telemetry was ingested by the Wazuh agent running on the Windows 11 endpoint. A custom decoder was used to parse the firewall log and extract fields including the source IP, destination IP, source port, destination port, protocol, and firewall action.

A custom Wazuh detection rule was then created to identify blocked TCP traffic originating from the Kali Linux attacker VM. The rule generated a **Level 8 alert** when the reconnaissance traffic was detected.

Key detection details:

- **Rule ID:** `100100`
- **Rule Level:** `8`
- **Source IP:** `192.168.134.130` (Kali Linux)
- **Destination IP:** `192.168.134.129` (WindowsSOC)
- **Protocol:** TCP
- **Firewall Action:** DROP
- **Decoder:** `windows-firewall`
- **MITRE ATT&CK:** `T1046`
- **Tactic:** Discovery

#### Wazuh Detection Evidence

![Wazuh custom reconnaissance detection](screenshots/detection-scenario/03-wazuh-custom-recon-detection.png)

*Figure 3 — Wazuh custom detection triggered by blocked TCP reconnaissance traffic from the Kali Linux attacker VM. The alert identifies the source and destination systems, firewall action, custom rule, severity level, and MITRE ATT&CK mapping.*

*Figure 2 — Windows Firewall telemetry showing dropped TCP connection attempts from the Kali Linux attacker VM (`192.168.134.130`) to the monitored Windows 11 endpoint (`192.168.134.129`).*
