# Cisco Network Security Configuration

## Overview
This repository records the build, configuration, verification, and security testing of a Cisco network using Cisco Packet Tracer. It contains actual device configurations, implementation notes, and evidence captured during testing.

## Network Inventory

| Device | Model | Quantity | Role |
|---|---|---:|---|
| Routers | Cisco 2911 | 2 | Connect the two LANs |
| Switches | Cisco 2960-24TT | 2 | Provide LAN connectivity |
| PCs | PC-PT | 4 | End-user and connectivity test devices |
| Servers | Server-PT | 2 | Server endpoints |

## Addressing Plan and Router Status

| Device/interface | IP address | Subnet mask | Status from latest output |
|---|---|---|---|
| Router1 G0/0 | 192.168.10.1 | 255.255.255.0 | up/up |
| Router1 G0/1 | 10.0.0.1 | 255.255.255.252 | up/up |
| Router2 G0/0 | 10.0.0.2 | 255.255.255.252 | up/up |
| Router2 G0/1 | 192.168.20.1 | 255.255.255.0 | up/up |

The intended physical mapping is Router1 G0/0 to Switch1, Router1 G0/1 to Router2 G0/0, and Router2 G0/1 to Switch2. The exact cable-to-port mapping should still be checked against the Packet Tracer workspace.

## Current Implementation Status
- Router1 G0/0 and G0/1 have IP addresses configured; the user-provided output reported both as up/up.
- Router2 G0/0 and G0/1 have IP addresses configured; the user-provided output reported both as up/up.
- PC and server IP settings: pending.
- Routing between the two LANs: not configured or verified.
- End-to-end connectivity: not tested.
- Security controls and IDS/IPS behavior: not implemented or verified.
- Screenshots: awaiting upload to `evidence/`.

## Repository Contents
- `configs/` — router configuration and verification records.
- `docs/` — implementation log.
- `evidence/` — screenshots captured from the actual Packet Tracer environment.

## Working Rules
1. Record only commands actually entered or results actually observed.
2. Keep planned configuration separate from verified results.
3. Verify interface state, addressing, routing, and traffic flow before claiming the network works.
4. Identify Packet Tracer feature limitations rather than presenting unsupported features as implemented.
