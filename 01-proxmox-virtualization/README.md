# 01 — Proxmox VE Virtualization Lab

**Category:** Server Administration / Virtualization  
**Status:** Proxmox installed; nested KVM verified in the temporary Windows test configuration

## 1. Project Overview

This lab deploys Proxmox VE 9.2.2 as a virtual machine in Oracle VirtualBox on a Windows 11 laptop. It demonstrates hypervisor installation, management access, virtual networking, and troubleshooting nested virtualization.

**Important environment note:** KVM was successfully verified after temporarily disabling the Windows hypervisor launch and restarting Windows. This changes the Windows virtualization-security configuration. KVM must be retested after Windows protections are restored; the successful result below applies to the temporary test configuration.

## 2. Objectives

- Install Proxmox VE and access its web management interface.
- Configure VirtualBox NAT and host-only adapters.
- Inspect Proxmox network bridges and node resources.
- Enable and verify nested Intel VT-x / KVM passthrough.
- Record troubleshooting steps and security trade-offs.

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

The first VirtualBox adapter uses NAT with port forwarding for management access. The second adapter is configured as a host-only interface for a future isolated lab network.

Inside Proxmox, the management interface is attached to bridge `vmbr0`. The second interface (`nic1`) is present but is not yet configured for lab traffic.

Management access was verified through the forwarded HTTPS port:

`https://127.0.0.1:8006`

The management IP is omitted from this public document. A sanitized topology diagram can be added under `architecture/` after the lab network is finalized.

## 5. Implementation Completed

1. Created the `PVE9-LAB` virtual machine in VirtualBox.
2. Allocated 2 vCPU, 4 GB RAM, and a 64 GB virtual disk.
3. Configured NAT port forwarding for SSH (host port 2222 to guest port 22) and Proxmox Web UI (host port 8006 to guest port 8006).
4. Added a VirtualBox host-only network adapter.
5. Installed Proxmox VE 9.2.2 from the official installation ISO.
6. Removed the installation ISO after setup and booted from the virtual disk.
7. Accessed the Proxmox web interface successfully.
8. Inspected Linux network interfaces and the `vmbr0` bridge.
9. Diagnosed Windows hypervisor / VBS interference with nested virtualization.
10. Temporarily changed Windows hypervisor launch configuration, restarted Windows, and verified KVM from inside Proxmox.

## 6. Testing and Results

| Test | Command / method | Observed result |
|---|---|---|
| Proxmox version | `pveversion` | `pve-manager/9.2.2` |
| Kernel | `uname -r` / version output | `7.0.2-6-pve` |
| Management UI | Browser via forwarded HTTPS port | Accessible |
| Bridge configuration | `ip a`, `/etc/network/interfaces` | `vmbr0` up; connected to `nic0` |
| CPU virtualization flag | `egrep -c '(vmx|svm)' /proc/cpuinfo` | `4` |
| KVM device | `ls -l /dev/kvm` | Device present: `/dev/kvm` |
| KVM kernel modules | `lsmod | grep kvm` | `kvm_intel`, `kvm`, and `irqbypass` loaded |
| Node status | `pvesh get /nodes/$(hostname)/status` | Node status returned successfully |

### KVM verification evidence

CPU virtualization flags:

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
# Output: 4
```

KVM device:

```bash
ls -l /dev/kvm
# Output observed:
# crw-rw---- 1 root kvm 10, 232 ... /dev/kvm
```

Loaded modules:

```bash
lsmod | grep kvm
# kvm_intel
# kvm
# irqbypass
```

These results confirm that Intel VT-x was exposed to the Proxmox guest and the KVM device was available during the temporary Windows test configuration.

## 7. Troubleshooting and Lessons Learned

### Initial issue: KVM acceleration unavailable

The Proxmox installer warned that hardware-accelerated KVM was not detected. Initial checks inside Proxmox showed:

- `egrep -c '(vmx|svm)' /proc/cpuinfo` returned `0`.
- `/dev/kvm` did not exist.
- Windows diagnostics reported a running hypervisor / Virtualization-Based Security.

### Diagnosis and resolution for the test

1. Verified Intel virtualization support in firmware and nested VT-x/AMD-V in VirtualBox.
2. Identified the Windows hypervisor / VBS configuration as a layer preventing nested virtualization passthrough in this setup.
3. Temporarily ran the following command in an elevated Windows Command Prompt:

   ```cmd
   bcdedit /set hypervisorlaunchtype off
   ```

4. Restarted Windows and confirmed that VBS was no longer running for the test.
5. Started the Proxmox VM and repeated the checks.
6. Confirmed four virtualization flags, the `/dev/kvm` device, and loaded KVM modules.

### Key lesson

Nested virtualization requires every layer to cooperate: firmware virtualization, host OS hypervisor configuration, VirtualBox nested VT-x passthrough, and the guest hypervisor. A setting enabled in VirtualBox does not alone guarantee that KVM will be available inside Proxmox.

## 8. Current Status and Next Steps

- [x] Proxmox VE installation
- [x] Proxmox Web UI access
- [x] Initial management bridge inspection
- [x] Diagnose Windows hypervisor interference
- [x] Verify Intel virtualization flags inside Proxmox
- [x] Verify `/dev/kvm` and KVM kernel modules
- [ ] Restore Windows virtualization security settings and retest KVM
- [ ] Configure and test the host-only lab network
- [ ] Deploy an Ubuntu Server guest
- [ ] Capture genuine screenshots and add a network diagram

## 9. Evidence

Place genuine, sanitized screenshots in `screenshots/`:

- VirtualBox VM system and network settings
- Proxmox web dashboard
- Proxmox version and kernel output
- `ip a` and bridge configuration
- Terminal output showing `vmx` count, `/dev/kvm`, and loaded KVM modules
- Windows security status after protections are restored and KVM retested

Do not publish passwords, tokens, private keys, or sensitive network details.
