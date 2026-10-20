# Cisco Network Security Configuration

## Overview
This repository documents the configuration, connectivity checks, and router ACL security testing of a Cisco Packet Tracer network.

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

The user reported successful router-to-router connectivity and successful cross-LAN pings before the ACL security test. The ACL test subsequently confirmed that PC1 could no longer ping Server2, while PC2 could still ping Server2. CLI ACL counters showed 5 matches on the deny entry and 1 match on the permit entry at the time of checking.

## Security Control

An extended IPv4 ACL named `BLOCK_PC1_TO_SERVER2` is applied outbound on Router2 GigabitEthernet0/1. It denies IP traffic from PC1 (192.168.10.10) to Server2 (192.168.20.20) and permits other IP traffic.

This demonstrates router ACL-based access control. It does **not** by itself implement a full IDS/IPS. An IOS IPS feature would need to be separately checked, configured, and tested if supported by this Packet Tracer device/version.

Router1 runs IOS 15.1(4)M4 with the `securityk9` evaluation package enabled. The `EDGE-IPS` rule is attached inbound to Router1 GigabitEthernet0/0. However, the latest checks are inconsistent: `show ip ips all` reports **1 active signature**, while `show ip ips signatures count` reports the `string-icmp` engine has **0 enabled and 1 retired signature**, despite the overall count showing 1 enabled. Signature load times are blank, and no IPS detection event has been confirmed. Therefore, IOS IPS detection is **not verified operationally** in this Packet Tracer image and must not be presented as a successful IPS test. See `docs/implementation-log.md` for troubleshooting details and `configs/Router2-security.txt` for the verified ACL configuration.

## Repository Contents
- `configs/` — router configuration and security command records.
- `docs/` — implementation log.
- `evidence/` — genuine screenshots captured from Packet Tracer.

## Evidence Status
The topology and router interface screenshots are listed in `evidence/README.md`. Screenshots for router connectivity, routing tables, and the ACL test should be added only after they are captured and committed.

## Working Rules
1. Record only commands actually entered or results actually observed.
2. Distinguish user-reported tests from CLI output or screenshots stored in this repository.
3. Keep planned configuration separate from verified results.
4. Identify Packet Tracer feature limitations rather than presenting unsupported features as implemented.
