# Cisco Network Security Configuration

## Overview
This repository documents the configuration, connectivity checks, and router ACL security testing of a Cisco Packet Tracer network. Configuration status is recorded separately from test results; an attached IPS rule alone is not proof of operational detection.

## Network Inventory

| Device | Model | Quantity | Role |
|---|---|---:|---|
| Routers | Cisco 2911 | 2 | Connect the two LANs |
| Switches | Cisco 2960-24TT | 2 | Provide LAN connectivity |
| PCs | PC-PT | 4 | End-user and connectivity test devices |
| Servers | Server-PT | 2 | Server endpoints |

## Addressing Plan

| Device/interface | IP address | Subnet mask | Role |
|---|---|---|---|
| Router1 G0/0 | 192.168.10.1 | 255.255.255.0 | LAN 1 gateway |
| Router1 G0/1 | 10.0.0.1 | 255.255.255.252 | Router-to-router link |
| Router2 G0/0 | 10.0.0.2 | 255.255.255.252 | Router-to-router link |
| Router2 G0/1 | 192.168.20.1 | 255.255.255.0 | LAN 2 gateway |

### End-device addressing

| Device | IP address | Default gateway |
|---|---|---|
| PC1 | 192.168.10.10/24 | 192.168.10.1 |
| PC2 | 192.168.10.11/24 | 192.168.10.1 |
| Server1 | 192.168.10.20/24 | 192.168.10.1 |
| PC3 | 192.168.20.10/24 | 192.168.20.1 |
| PC4 | 192.168.20.11/24 | 192.168.20.1 |
| Server2 | 192.168.20.20/24 | 192.168.20.1 |

## Routing and Connectivity

Static routes are configured:
- Router1: `ip route 192.168.20.0 255.255.255.0 10.0.0.2`
- Router2: `ip route 192.168.10.0 255.255.255.0 10.0.0.1`

### Latest connectivity checkpoint — 2026-10-25
The user reported successful pings from Router1 to PC2 (`192.168.10.11`), Router2's transit interface (`10.0.0.2`), and Router2's LAN interface (`192.168.20.1`). Router2 also successfully pinged Server2 (`192.168.20.20`) and PC2 (`192.168.10.11`).

PC2-to-Server2 ping timed out while the Router2 ACL was detached from its interface. After the IPS rule was also detached from Router1 G0/0, PC2-to-Server2 succeeded with 4 replies and 0% loss. This implicates the IPS interface attachment in the earlier failure, but the exact cause is not yet confirmed because the signature status is inconsistent and both security controls were detached by the time the successful ping was recorded.

## Security Controls

### Router2 ACL: `BLOCK_PC1_TO_SERVER2`
The ACL definition contains:
- Deny IP traffic from PC1 (`192.168.10.10`) to Server2 (`192.168.20.20`).
- Permit all other IP traffic.

**Current state:** the ACL definition remains on Router2, but it is temporarily detached from GigabitEthernet0/1 during troubleshooting. The intended interface command is `ip access-group BLOCK_PC1_TO_SERVER2 out`. Re-apply it and verify PC2 is permitted and PC1 is blocked before claiming the ACL test is currently active. See `configs/Router2-security.txt` and `docs/implementation-log.md`.

### Router1 IOS IPS: `EDGE-IPS`
Router1 runs IOS 15.1(4)M4 with the `securityk9` evaluation package enabled. The IPS signature status was inconsistent: `show ip ips all` reported one active signature, while `show ip ips signatures count` showed the `string-icmp` engine had zero enabled and one retired signature. Signature load times were blank, and no IPS detection event was confirmed.

**Current state:** the IPS rule is temporarily detached from Router1 GigabitEthernet0/0 using `no ip ips EDGE-IPS in`. The IPS definition has not been deleted. Do not claim operational IPS detection until valid signature status and an actual detection event are verified.

## Repository Contents
- `configs/` — router configuration and security command records.
- `docs/` — implementation log and troubleshooting checkpoints.
- `evidence/` — genuine screenshots captured from Packet Tracer.

## Evidence Status
The evidence index is in `evidence/README.md`. Screenshots for connectivity recovery, routing tables, ACL configuration/counters, and the final PC1-blocked / PC2-allowed test still need to be captured and committed.

## Working Rules
1. Record only commands actually entered or results actually observed.
2. Distinguish user-reported tests from CLI output or screenshots stored in this repository.
3. Keep planned configuration separate from verified results.
4. Identify Packet Tracer feature limitations rather than presenting unsupported features as implemented.
