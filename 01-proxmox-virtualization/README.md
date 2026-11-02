# 01 — Proxmox VE Virtualization Lab

**Category:** Server Administration / Virtualization  
**Status:** Installation completed; nested KVM troubleshooting in progress

## 1. Project Overview

This lab deploys Proxmox VE 9.2.2 as a guest virtual machine in Oracle VirtualBox on a Windows 11 laptop. The purpose is to practice hypervisor installation, management access, virtual networking, and virtualization troubleshooting.

> **Lab limitation:** Proxmox is running inside VirtualBox (nested virtualization). The current verification did not expose Intel VT-x to the Proxmox guest, so KVM acceleration is not yet available. This is recorded as an unresolved limitation rather than a successful KVM test.

## 2. Objectives

- Install Proxmox VE and access its web management interface.
- Configure the management bridge and VirtualBox network adapters.
- Inspect CPU virtualization flags and KVM device availability.
- Diagnose nested virtualization constraints between Windows, VirtualBox, and Proxmox.
- Continue with guest VM deployment after the KVM / resource constraints are resolved.

## 3. Lab Environment

| Component | Actual value |
|---|---|
| Host OS | Windows 11 Home Single Language |
| Host CPU | Intel Processor N200, 4 logical processors |
| Host RAM | 8 GB |
| VirtualBox | 7.2.22 |
| Proxmox VE | 9.2.2 |
| Proxmox kernel | 7.0.2-6-pve |
| Proxmox VM allocation | 2 vCPU, 4 GB RAM |
| Virtual disk | 64 GB dynamically allocated VDI |
| VM firmware | BIOS (EFI disabled) |
| VM network adapters | Adapter 1: NAT; Adapter 2: Host-only |
| Proxmox management bridge | vmbr0 connected to nic0 |

## 4. Network Design

The first VirtualBox adapter uses NAT and port forwarding for management access. The second adapter is configured as a host-only interface for a future isolated lab network.

Inside Proxmox, the management interface is attached to bridge `vmbr0`. The second interface (`nic1`) is present but was not yet configured for lab traffic.

Management access was verified through the forwarded HTTPS port at:

`https://127.0.0.1:8006`

The guest management address and host-only addressing are intentionally omitted from this public document. Add a sanitized topology diagram under `architecture/` when the lab network is finalized.

## 5. Implementation Completed

1. Created the `PVE9-LAB` virtual machine in VirtualBox.
2. Allocated 2 vCPU, 4 GB RAM, and a 64 GB virtual disk.
3. Configured NAT port forwarding for SSH (host port 2222 to guest port 22) and Proxmox Web UI (host port 8006 to guest port 8006).
4. Added a VirtualBox host-only network adapter.
5. Installed Proxmox VE 9.2.2 from the official installation ISO.
6. Removed the installation ISO after setup and booted from the virtual disk.
7. Accessed the Proxmox web interface successfully.
8. Inspected Linux network interfaces and the `vmbr0` bridge.

## 6. Testing and Results

| Test | Command / method | Observed result |
|---|---|---|
| Proxmox version | `pveversion` | `pve-manager/9.2.2` |
| Kernel | `uname -r` / version output | `7.0.2-6-pve` |
| Management UI | Browser via forwarded HTTPS port | Accessible |
| Bridge configuration | `ip a`, `/etc/network/interfaces` | `vmbr0` up; connected to `nic0` |
| CPU virtualization flag in Proxmox | `egrep -c '(vmx|svm)' /proc/cpuinfo` | `0` at the time of test |
| KVM device | `ls -l /dev/kvm` | Not present at the time of test |

## 7. Troubleshooting and Lessons Learned

### KVM acceleration unavailable inside Proxmox

The Proxmox installer displayed a warning that hardware-accelerated KVM virtualization was not detected. After installation, the following checks confirmed that the guest did not receive the Intel virtualization flag and that the KVM device was absent:

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
ls -l /dev/kvm
```

Observed output:
- CPU virtualization flag count: `0`
- `/dev/kvm`: No such file or directory

VirtualBox settings showed nested VT-x/AMD-V enabled, but that setting alone did not prove that VT-x was exposed to the Proxmox guest. Windows diagnostics also reported that a hypervisor was detected and that Virtualization-Based Security was running.

Further troubleshooting is in progress. No claim is made that KVM acceleration has been enabled.

### Key lesson

Nested virtualization depends on the host CPU, firmware, host hypervisor, Windows virtualization security configuration, and VirtualBox passthrough support. Each layer must be verified independently.

## 8. Current Status and Next Steps

- [x] Proxmox VE installation
- [x] Boot from virtual disk after installation
- [x] Proxmox Web UI access
- [x] Initial management bridge inspection
- [x] Nested virtualization diagnostics captured
- [ ] Resolve or document the KVM passthrough limitation
- [ ] Verify `vmx` CPU flag and `/dev/kvm` if KVM becomes available
- [ ] Configure and test the host-only lab network
- [ ] Deploy an Ubuntu Server guest (subject to KVM and host resource constraints)
- [ ] Capture genuine screenshots and add a network diagram

## 9. Evidence

Place genuine, sanitized screenshots in `screenshots/`:

- VirtualBox VM system and network settings
- Proxmox web dashboard
- Proxmox version and kernel output
- `ip a` and bridge configuration
- KVM diagnostic output showing the initial limitation
- Follow-up KVM test results, if the issue is resolved

Do not publish passwords, tokens, private keys, or sensitive network details.
