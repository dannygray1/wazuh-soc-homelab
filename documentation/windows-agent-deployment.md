# Windows 11 Wazuh Agent Deployment and Validation

**Environment:** Oracle VirtualBox  
**Endpoint:** SOC-WIN11-01  
**Operating System:** Windows 11 Enterprise Evaluation  
**Wazuh Agent Version:** 4.14.9  
**Wazuh Manager:** SOC-WAZUH-01 (192.168.56.10)  
**Status:** Completed

## Objective

Deploy and enroll a Windows 11 endpoint into the Wazuh SIEM environment to enable centralized collection, monitoring, and analysis of Windows security events.

## Endpoint Configuration

| Component | Configuration |
|---|---|
| Virtual Machine | SOC-WIN11-01 |
| Operating System | Windows 11 Enterprise Evaluation |
| Virtualization Platform | Oracle VirtualBox |
| Memory | 4 GB RAM |
| Processors | 2 virtual CPUs |
| Internal Network | SOC-LAB |
| Internal IP Address | 192.168.56.20 |
| Wazuh Manager IP | 192.168.56.10 |
| Wazuh Agent ID | 001 |
| Agent Group | default |

The Windows endpoint and Ubuntu Wazuh server communicate through the isolated VirtualBox internal network.

## Agent Installation

The Windows Wazuh agent was installed using the official Wazuh MSI package and PowerShell.

During installation, the following deployment settings were specified:

- Wazuh Manager Address: `192.168.56.10`
- Agent Name: `SOC-WIN11-01`
- Agent Group: `default`

After installation, the Wazuh agent service was started using PowerShell:

```powershell
Start-Service WazuhSvc
```

The Windows endpoint was then checked in the Wazuh dashboard.

## Agent Enrollment and Connectivity

The endpoint successfully enrolled with the Wazuh manager and appeared in the dashboard with the following details:

| Field | Value |
|---|---|
| Agent Name | SOC-WIN11-01 |
| Agent ID | 001 |
| Agent IP | 192.168.56.20 |
| Agent Status | Active |

The active status confirmed that the endpoint was successfully communicating with the Wazuh manager.

## Security Monitoring Validation

After enrollment, Windows security telemetry became available through Wazuh Threat Hunting.

The initial monitoring review included:

- Windows service configuration changes.
- Security Configuration Assessment (SCA) findings.
- Windows authentication-related security events.

A Windows Service Control Manager Event ID `7040` was reviewed during baseline monitoring.

The event reported a change to the Background Intelligent Transfer Service (BITS) startup configuration.

The service state and startup mode were inspected using:

```powershell
Get-CimInstance Win32_Service -Filter "Name='BITS'" |
Select-Object Name, DisplayName, State, StartMode
```

The service was observed in a stopped state with a manual startup configuration.

The cause of the change was not established, and the event alone did not demonstrate malicious activity.

## Security Configuration Assessment

Wazuh reported results for the CIS Windows 11 Enterprise Benchmark v3.0.0.

The initial assessment showed:

| Result | Count |
|---|---|
| Passed | 123 |
| Failed | 350 |
| Not Applicable | 9 |
| Reported Score | 26% |

These results represent the endpoint's initial security configuration assessment.

The assessment findings identify potential configuration improvements and should not be interpreted as evidence of a system compromise.

## Findings

- The Windows Wazuh agent was successfully installed.
- The endpoint enrolled with the Wazuh manager.
- Agent ID `001` was assigned.
- The endpoint appeared as active in the dashboard.
- Windows security and service-related events were visible in Wazuh.
- Baseline configuration assessment results were available for review.

## Lessons Learned

- Endpoint agents provide the telemetry required for centralized security monitoring.
- Successful installation does not by itself confirm connectivity; agent status must also be validated.
- Windows Event IDs and Wazuh rule IDs serve different purposes.
- Service configuration changes can be legitimate administrative or operating system activity and require contextual investigation.
- Security Configuration Assessment findings are configuration compliance indicators, not direct evidence of malicious behavior.

## Outcome

**Windows 11 Wazuh agent deployment completed successfully.**

The endpoint was enrolled, actively communicating with the Wazuh manager, and forwarding security telemetry.

This established the foundation for controlled security event generation and SOC alert investigation.
