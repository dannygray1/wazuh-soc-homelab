# Network Configuration and Connectivity Validation

**Environment:** Oracle VirtualBox  
**Project:** Wazuh SOC Home Lab  
**Operating Systems:** Ubuntu Server 24.04.5 LTS and Windows 11 Enterprise Evaluation  
**Status:** Completed

## Objective

Configure network connectivity between the Ubuntu-based Wazuh server and Windows 11 endpoint while maintaining a separate network for lab communication.

The configuration uses two virtual network adapters on each machine: one for external connectivity and another for isolated communication between the SOC lab systems.

## Lab Network Architecture

| Virtual Machine | Network Adapter | Network Type | IP Address |
|---|---|---|---|
| SOC-WAZUH-01 | Adapter 1 | NAT | 10.0.2.15 (DHCP) |
| SOC-WAZUH-01 | Adapter 2 | Internal Network (SOC-LAB) | 192.168.56.10/24 |
| SOC-WIN11-01 | Adapter 1 | NAT Network (TCM) | 192.168.1.3 (DHCP) |
| SOC-WIN11-01 | Adapter 2 | Internal Network (SOC-LAB) | 192.168.56.20/24 |

The `SOC-LAB` internal network allows the Windows endpoint to communicate directly with the Wazuh server without exposing that interface to the physical network.

## Ubuntu Server Network Configuration

Ubuntu Server networking was configured using Netplan.

The following configuration enables DHCP on the NAT adapter and assigns a static address to the internal network adapter.

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 192.168.56.10/24
```

After editing the configuration, network settings were applied and verified.

```bash
sudo netplan apply
ip -br addr
```

The Ubuntu server was confirmed to have both its NAT address and the static internal network address.

## Windows 11 Network Configuration

The Windows 11 virtual machine was configured with two network adapters.

The internal network adapter was assigned the following IPv4 configuration:

| Setting | Value |
|---|---|
| IP Address | 192.168.56.20 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | Not configured on the internal adapter |
| DNS Server | Not required on the internal adapter |

The NAT Network adapter remained available for external connectivity.

Windows networking was verified using:

```powershell
ipconfig /all
```

## Connectivity Testing

Connectivity was tested between both virtual machines.

From Windows 11:

```powershell
ping 192.168.56.10
```

From Ubuntu Server:

```bash
ping -c 4 192.168.56.20
```

The Windows-to-Ubuntu test succeeded.

The initial Ubuntu-to-Windows test did not receive responses because Windows was not permitting inbound ICMP echo requests.

## Windows Firewall Configuration

An inbound Windows Defender Firewall rule was configured to permit ICMPv4 echo requests for connectivity testing.

The rule was restricted to the lab network addresses rather than broadly allowing ICMP traffic from all sources.

After configuring the rule, connectivity was tested again.

**Result:** Successful bidirectional ICMP communication between Ubuntu Server and Windows 11.

## SSH Remote Administration

SSH access was configured to allow administration of the Ubuntu server from the physical Windows host.

VirtualBox NAT port forwarding was configured as follows:

| Setting | Value |
|---|---|
| Protocol | TCP |
| Host IP | 127.0.0.1 |
| Host Port | 2222 |
| Guest IP | 10.0.2.15 |
| Guest Port | 22 |

An Ed25519 SSH key pair was generated on the physical Windows host. The public key was added to the Ubuntu user's `authorized_keys` file.

The following PowerShell command was used to establish the SSH connection:

```powershell
ssh -i "$env:USERPROFILE\.ssh\wazuh_soc_lab_ed25519" -p 2222 danny@127.0.0.1
```

The SSH connection was tested successfully.

The private SSH key was retained on the physical Windows host and was not uploaded to GitHub.

### Wazuh Dashboard Access via SSH Tunnel

The Wazuh dashboard was accessed securely from the physical Windows host using SSH local port forwarding.

The following PowerShell command established the tunnel:

```powershell
ssh -i "$env:USERPROFILE\.ssh\wazuh_soc_lab_ed25519" -p 2222 -N -L 8443:127.0.0.1:443 danny@127.0.0.1
```

After establishing the connection, the Wazuh dashboard was accessible through a web browser at:

`https://127.0.0.1:8443`

This configuration forwarded traffic from the physical Windows host to the Wazuh dashboard running on the Ubuntu server without requiring direct dashboard access from the physical network.

The dashboard used a self-signed TLS certificate, resulting in an expected browser certificate warning.

## Findings

- Both virtual machines successfully obtained external network connectivity through their respective NAT configurations.
- Static IP addresses were configured on the isolated internal network.
- Bidirectional ICMP connectivity was established after adjusting the Windows firewall configuration.
- SSH access to Ubuntu Server was successfully configured using VirtualBox port forwarding and public-key authentication.
- The isolated network provided a dedicated communication path between the Windows endpoint and Wazuh server.

## Lessons Learned

- VirtualBox NAT and Internal Network adapters serve different purposes and can be combined in a multi-adapter lab configuration.
- Static IP addressing simplifies communication between infrastructure components.
- A failed ping does not necessarily indicate a routing or network configuration problem; host firewall rules can also prevent ICMP responses.
- Restricting firewall rules to known lab addresses reduces unnecessary exposure.
- SSH public-key authentication enables remote administration without sharing the private key.

## Outcome

**Network configuration completed successfully.**

The Ubuntu Wazuh server and Windows 11 endpoint were able to communicate across the isolated `SOC-LAB` network, and remote SSH administration was verified.

This configuration established the network foundation required for Wazuh agent deployment and security event monitoring.
