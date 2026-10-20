# Troubleshooting Note: Ubuntu Server Installation Stalled

**Environment:** Oracle VirtualBox  
**Virtual Machine:** SOC-WAZUH-01  
**Operating System:** Ubuntu Server 24.04.5 LTS  
**Date:** October 19, 2026  
**Status:** Resolved

## Issue Description

During the initial installation of Ubuntu Server 24.04.5 LTS, the installer became unresponsive at the following step:

`copying metadata from /cdrom`

The installation screen remained unchanged for an extended period and did not display the expected completion or reboot options.

## Troubleshooting Steps

1. Waited to determine whether the installation would complete normally.
2. Accessed an alternate terminal (TTY2) to verify that the operating system environment remained responsive.
3. Reviewed active processes using `ps aux` and confirmed that the Subiquity installer was still running.
4. Examined the installer logs located at `/var/log/installer/`.
5. Confirmed that the base operating system installation, GRUB installation, and bootloader configuration had reported successful completion.
6. Identified that the installer remained in a `WAITING` state.
7. Inspected `/target/home` and `/target/etc/passwd` and found that the expected user account had not been created.

## Findings

The installation logs indicated that the base Ubuntu operating system and bootloader had been installed successfully. However, the installer did not complete its remaining configuration steps or transition to the final reboot screen.

No definitive root cause was identified. The incomplete user configuration suggested that the installation had not fully finalized.

## Resolution

Rather than continuing to troubleshoot the incomplete installation, I performed a clean reinstallation of Ubuntu Server 24.04.5 LTS using the existing VirtualBox virtual machine.

The second installation completed successfully.

After rebooting, I verified the operating system version and system resources using:

```bash
cat /etc/os-release
free -h
df -h /
nproc
```

The system was confirmed to be running Ubuntu Server 24.04.5 LTS with approximately 8 GB RAM, 60 GB virtual disk capacity, and 4 virtual CPUs.

A clean installation snapshot named `Ubuntu-24.04-Clean-Install` was created in VirtualBox to establish a recovery point before further configuration.

## Lessons Learned

- Installer progress screens do not always accurately reflect the status of underlying installation processes.
- Reviewing installation logs can help distinguish completed tasks from incomplete configuration steps.
- Verifying user accounts and system configuration is important before assuming an installation succeeded.
- Clean VM snapshots provide useful recovery points before making significant configuration changes.
- Reinstallation can be a practical recovery strategy when troubleshooting costs outweigh the effort required to rebuild an early-stage lab environment.

**Outcome:** Ubuntu Server 24.04.5 LTS successfully installed, verified, and prepared for the next phase of the Wazuh SOC home lab.
