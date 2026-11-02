# 05 — Secure Remote Access with WireGuard VPN

**Category:** Network Security / Remote Access  
**Status:** Documentation template — evidence to be added

## 1. Project Overview

Document a WireGuard VPN lab connecting a client and server through an encrypted
tunnel.

## 2. Objectives

- Install WireGuard on the lab server and client.
- Create and configure peer keys safely.
- Configure tunnel addressing and routing.
- Verify tunnel status and permitted connectivity.
- Record troubleshooting and security considerations.

## 3. Lab Environment

| Component | Actual value |
|---|---|
| Server OS | [TODO] |
| WireGuard version | [TODO] |
| Client OS | [TODO] |
| Tunnel address plan | [TODO — sanitized example only] |
| Network path | [TODO] |

## 4. Topology

Add a diagram to `topology/`:

`![VPN topology](topology/vpn-topology.png)`

## 5. Implementation

[TODO: describe installation, key generation, peer configuration, routing,
firewall rules, and startup procedure. Do not include actual keys.]

## 6. Testing and Results

| Test | Method | Actual result |
|---|---|---|
| Interface active | `wg show` | [TODO] |
| Handshake observed | `wg show` | [TODO] |
| Tunnel connectivity | ping / approved test | [TODO] |
| Intended route works | [TODO] | [TODO] |

## 7. Evidence

Add screenshots with public keys, endpoint details, IPs, and identifying
information masked as appropriate. **Never publish private keys.**

## 8. Troubleshooting and Lessons Learned

[TODO]

## 9. Security Notes

Private keys are secrets. Do not commit client or server private keys,
complete live configuration files, credentials, or sensitive endpoints.
