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
