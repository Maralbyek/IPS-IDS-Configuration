# Cisco Network Security Configuration

## Overview
This repository records the build, configuration, verification, and security testing of a Cisco network using Cisco Packet Tracer. It will contain actual device configurations, topology evidence, and test results as they are produced.

The record distinguishes between work completed, work verified, and work still pending. Unsupported Packet Tracer features will not be presented as implemented.

## Current Network

| Device | Model | Quantity | Role |
|---|---|---:|---|
| Routers | Cisco 2911 | 2 | Connect the two network segments |
| Switches | Cisco 2960-24TT | 2 | Provide LAN connectivity |
| PCs | PC-PT | 4 | End-user and connectivity test devices |
| Servers | Server-PT | 2 | Server endpoints |

## Current Implementation Status
- Router 1: GigabitEthernet0/0 and GigabitEthernet0/1 have been issued `no shutdown`.
- Router 2: GigabitEthernet0/0 and GigabitEthernet0/1 have been issued `no shutdown`.
- Router 2 verification: both interfaces reported `up/up` in `show ip interface brief`.
- IP addressing and routing: not configured yet.
- End-to-end connectivity: not tested yet.
- Security controls and IDS/IPS behavior: not implemented or verified yet.
- Packet Tracer screenshot evidence: to be added.

## Repository Contents
- `configs/` — initial router command records.
- `docs/` — implementation log and verification notes.
- `evidence/` — screenshots and captured verification output from the actual Packet Tracer environment.

## Working Rules
1. Record only commands actually entered or results actually observed.
2. Keep device configurations and verification output separate where useful.
3. Verify interface state, addressing, routing, and traffic flow before claiming the network works.
4. Clearly identify limitations of the Packet Tracer model and its supported security features.

## Environment
- Cisco Packet Tracer
- Cisco 2911 routers
- Cisco 2960-24TT switches
