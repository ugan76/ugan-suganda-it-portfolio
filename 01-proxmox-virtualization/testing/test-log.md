# Proxmox VE Lab — Test Log

This file records tests actually performed in the lab. Results are snapshots from the test session and may change after Windows security settings or VM configuration changes.

## Test environment

- Host: Windows 11 Home Single Language
- CPU: Intel Processor N200, 4 logical processors
- Host memory: 8 GB
- Hypervisor: Oracle VirtualBox 7.2.22
- Proxmox: VE 9.2.2
- Proxmox kernel: 7.0.2-6-pve
- Proxmox VM allocation: 2 vCPU, 4096 MB RAM, 64 GB virtual disk

## Test cases

| ID | Test | Command / method | Result | Status |
|---|---|---|---|---|
| T01 | Proxmox management UI | Browser: `https://127.0.0.1:8006` | Login/dashboard accessible | PASS |
| T02 | Proxmox version | `pveversion` | `pve-manager/9.2.2` | PASS |
| T03 | Proxmox kernel | `uname -r` | `7.0.2-6-pve` | PASS |
| T04 | CPU virtualization flags | `egrep -c '(vmx|svm)' /proc/cpuinfo` | Output: `4` | PASS |
| T05 | KVM device node | `ls -l /dev/kvm` | Character device present | PASS |
| T06 | KVM modules | `lsmod | grep kvm` | `kvm_intel`, `kvm`, `irqbypass` loaded | PASS |
| T07 | Node status | `pvesh get /nodes/$(hostname)/status` | Status table returned | PASS |
| T08 | Host-only network connectivity | Ping / route test | Not yet tested | PENDING |
| T09 | Ubuntu Server guest lifecycle | Create, start, stop guest | Not yet tested | PENDING |
| T10 | KVM after Windows security restore | Repeat T04–T06 after restoring VBS / Memory Integrity | Not yet tested | PENDING |

## Key command output

### CPU virtualization flags

```text
$ egrep -c '(vmx|svm)' /proc/cpuinfo
4
```

### KVM device

```text
$ ls -l /dev/kvm
crw-rw---- 1 root kvm 10, 232 ... /dev/kvm
```

### Loaded kernel modules

```text
$ lsmod | grep kvm
kvm_intel
kvm
irqbypass
```

## Resource snapshot

The node status command returned the following approximate values during the captured session:

| Resource | Observed value |
|---|---:|
| Proxmox VM memory total | 3.82 GiB |
| Memory used | 1.60 GiB (approx.) |
| Memory available | 2.22 GiB (approx.) |
| Root filesystem total | 26.32 GiB |
| Root filesystem used | 4.00 GiB (approx.) |
| Root filesystem available | 20.96 GiB (approx.) |

Values are rounded from the command output and represent the Proxmox VM's reported resources, not the physical laptop's full capacity.

## Test conditions and caveat

The successful KVM checks were performed after temporarily setting the Windows host boot option `hypervisorlaunchtype` to `off` and restarting Windows. This was a troubleshooting test, not a recommendation to leave Windows security features disabled.

Restore Windows virtualization security settings for normal use, then repeat T04–T06. If KVM is no longer exposed, record that outcome and use a suitable alternative host for nested virtualization.

## Evidence

Screenshots are stored in the sibling `screenshots/` directory. Refer to the project README for the evidence gallery.
