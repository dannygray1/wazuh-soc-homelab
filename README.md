# Wazuh SOC Home Lab

## Project Overview

This project documents the development of a virtual Security Operations Center (SOC) home lab using Wazuh SIEM, Ubuntu Server, Windows 11, and Oracle VirtualBox.

The goal is to gain hands-on experience with security monitoring, log analysis, threat detection, incident investigation, and technical documentation.

**Project Status:** In Progress — Environment Setup

## Lab Architecture

| Component | Technology | Purpose |
|---|---|---|
| Hypervisor | Oracle VirtualBox | Host virtual lab machines |
| SIEM Server | Wazuh on Ubuntu Server 24.04 LTS | Centralized security monitoring and analysis |
| Endpoint | Windows 11 VM | Generate and forward security events |
| Log Source | Windows Event Logs | Provide authentication and security telemetry |

*The architecture above represents the planned deployment. Components will be documented and validated as they are configured.*

## Project Objectives

- Deploy and configure a Wazuh SIEM environment.
- Connect a Windows 11 endpoint using the Wazuh agent.
- Collect and analyze Windows security events.
- Simulate failed authentication attempts in a controlled lab.
- Investigate Windows Event ID 4625 (failed logon).
- Document detection results, investigation steps, and findings.

## Project Phases

| Phase | Description | Status |
|---|---|---|
| 1 | Virtual machine and Ubuntu Server setup | In Progress |
| 2 | Virtual networking and SSH configuration | Planned |
| 3 | Wazuh installation and configuration | Planned |
| 4 | Windows 11 agent deployment | Planned |
| 5 | Security event simulation and investigation | Planned |
| 6 | Incident report and final documentation | Planned |

## Skills Demonstrated

This project is designed to develop practical experience in:

- SIEM deployment and administration
- Windows Event Log analysis
- Security alert triage
- Endpoint monitoring
- Network configuration
- Incident investigation and reporting
- Linux server administration

## Documentation

Installation procedures, configuration screenshots, troubleshooting notes, and investigation findings will be added as the project progresses.

## Disclaimer

This lab is built for educational purposes using isolated virtual machines. All security testing will be performed against systems owned and controlled within the lab.
