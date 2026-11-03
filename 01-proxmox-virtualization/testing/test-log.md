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
| T08 | Host-only network connectivity | Bidirectional ping between Windows `192.168.56.1` and Proxmox `192.168.56.10` | Both directions succeeded after allowing inbound ICMP on Windows Firewall | PASS |
| T09 | Ubuntu Server guest lifecycle | Create, start, stop guest | Not tested; KVM is unavailable with current Windows security configuration | PENDING |
| T10 | KVM after Windows security restore | Repeat T04–T06 with VBS / HVCI / Memory Integrity active | `vmx/svm` = `0`; `/dev/kvm` absent; no KVM modules shown | NOT AVAILABLE |
| T11 | Host-only TCP Web UI | Windows `Test-NetConnection 192.168.56.10 -Port 8006` | TCP connection succeeded | PASS |
| T12 | Host-only TCP SSH | Windows `Test-NetConnection 192.168.56.10 -Port 22` | TCP connection succeeded | PASS |
| T13 | SSH login over Host-only | `ssh root@192.168.56.10` | Login succeeded | PASS |
| T14 | Proxmox storage status | `pvesm status` | `local` and `local-lvm` active | PASS |
| T15 | Block device layout | `lsblk` | 64 GB `/dev/sda`; LVM volumes visible | PASS |
| T16 | Filesystem capacity | `df -h` | `/` 27 GB total, 4.1 GB used, 21 GB available | PASS |

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


## Host-only network configuration

| Endpoint | Interface | IPv4 address |
|---|---|---|
| Windows host | VirtualBox Host-Only Ethernet Adapter | `192.168.56.1/24` |
| Proxmox | `nic1` | `192.168.56.10/24` |
| Proxmox NAT bridge | `vmbr0` | `10.0.2.15/24` (lab NAT segment) |

Windows Defender Firewall initially blocked ping requests initiated by Proxmox. Connectivity was restored by allowing the required inbound ICMPv4 Echo Request traffic. The firewall was not left disabled. Both ping directions were then successful.

Windows could reach Proxmox TCP ports 8006 and 22, and SSH login over the Host-only address succeeded.

## KVM test across host security configurations

### Temporary test configuration — PASS

When Windows hypervisor launch was temporarily set to off and Windows was restarted:

```text
vmx/svm count: 4
/dev/kvm: present
Modules: kvm_intel, kvm, irqbypass
```

This confirms that KVM was successfully exposed to Proxmox under that temporary test configuration.

### Restored Windows security configuration — NOT AVAILABLE

After restoring `bcdedit /set hypervisorlaunchtype auto`, and confirming VBS/HVCI and Memory Integrity were running, the tests returned:

```text
egrep -c '(vmx|svm)' /proc/cpuinfo
0

ls -l /dev/kvm
No such file or directory

lsmod | grep kvm
(no output)
```

Interpretation: KVM was previously verified successfully, but is not exposed to the Proxmox guest with the current Windows virtualization-security configuration. This is an environment limitation observed in this lab, not evidence that the Proxmox installation itself failed. Windows security protections remain enabled. Do not treat disabling those protections as the default workaround.

## Storage inspection snapshot

```text
pvesm status:
local       dir      active   total 27605956 KiB   used 4212624 KiB   available 21965672 KiB
local-lvm   lvmthin  active   total 23990272 KiB   used       0 KiB   available 23990272 KiB

lsblk:
sda     64G
├─sda1  1007K
├─sda2  512M
└─sda3  63.5G
  ├─pve-swap 3.8G
  ├─pve-root 26.9G
  ├─pve-data 22.9G
  └─pve-data_tmeta 1G

df -h:
root filesystem: 27G total, 4.1G used, 21G available
```

These values are point-in-time observations and may change as the lab is used.
