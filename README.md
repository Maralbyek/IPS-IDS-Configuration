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

The user reported successful router-to-router and cross-LAN router pings during troubleshooting. PC2-to-Server2 connectivity was restored after detaching the Router1 IPS rule and remains successful with the Router2 ACL attached.

## Security Controls

### Router2 ACL: `BLOCK_PC1_TO_SERVER2`

The ACL contains:
- Deny IP traffic from PC1 (`192.168.10.10`) to Server2 (`192.168.20.20`).
- Permit all other IP traffic.

**Current state: attached and verified on Router2 GigabitEthernet0/1 (outbound).**

Latest test results (2026-10-25):
- PC2 (`192.168.10.11`) → Server2: successful, 4/4 replies (0% loss) on the second ping run.
- PC1 (`192.168.10.10`) → Server2: blocked; the ping returned `Destination host unreachable` from `10.0.0.2`.
- Router2 `show access-lists`: deny entry recorded **4 matches**; permit entry recorded **7 matches**.

The deny counter confirms the ACL matched PC1's blocked traffic. These results were provided as CLI output in the conversation; corresponding screenshots still need to be captured and committed.

### Router1 IOS IPS: `EDGE-IPS`

Router1 runs IOS 15.1(4)M4 with the `securityk9` evaluation package enabled. IPS signature status was inconsistent: `show ip ips all` reported one active signature, while `show ip ips signatures count` showed the `string-icmp` engine had zero enabled and one retired signature. Signature load times were blank, and no IPS detection event was confirmed.

**Current state: the IPS rule is temporarily detached from Router1 GigabitEthernet0/0** using `no ip ips EDGE-IPS in`. The definition has not been deleted. Keep it detached until a controlled test can be run with valid signature status. Do not claim operational IPS detection until an actual event is verified.

## Repository Contents
- `configs/` — router configuration and security command records.
- `docs/` — implementation log and troubleshooting checkpoints.
- `evidence/` — genuine screenshots captured from Packet Tracer.

## Evidence Status
The evidence index is in `evidence/README.md`. Screenshots for the topology, routing tables, ACL configuration/counters, and PC1-blocked / PC2-allowed ping tests still need to be captured and committed.

## Working Rules
1. Record only commands actually entered or results actually observed.
2. Distinguish user-reported tests from CLI output or screenshots stored in this repository.
3. Keep planned configuration separate from verified results.
4. Identify Packet Tracer feature limitations rather than presenting unsupported features as implemented.
