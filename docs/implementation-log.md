# Implementation Log

## Checkpoint 1 — Initial device setup
- Inventory: two Cisco 2911 routers, two Cisco 2960-24TT switches, four PC-PT endpoints, and two Server-PT endpoints.
- Issued `no shutdown` on GigabitEthernet0/0 and GigabitEthernet0/1 on both routers.

## Checkpoint 2 — Router interface addressing

### Addressing applied
| Device/interface | IP address | Subnet mask | Intended role |
|---|---|---|---|
| Router1 G0/0 | 192.168.10.1 | 255.255.255.0 | LAN 1 gateway |
| Router1 G0/1 | 10.0.0.1 | 255.255.255.252 | Router-to-router link |
| Router2 G0/0 | 10.0.0.2 | 255.255.255.252 | Router-to-router link |
| Router2 G0/1 | 192.168.20.1 | 255.255.255.0 | LAN 2 gateway |

### Verification reported by user
- Router1 `show ip interface brief`: G0/0 = 192.168.10.1 up/up; G0/1 = 10.0.0.1 up/up.
- Router2 `show ip interface brief`: G0/0 = 10.0.0.2 up/up; G0/1 = 192.168.20.1 up/up.

### Pending
- Confirm exact physical cable-to-port mapping against the Packet Tracer workspace.
- Configure PC and server IP addresses and default gateways.
- Configure routing between the LANs.
- Test local and end-to-end connectivity; record actual results.
- Implement and verify appropriate security controls supported by the selected Packet Tracer devices.
- Add screenshots of the topology and CLI verification output under `evidence/`.

## Next checkpoint
Configure PC1, verify its settings, then continue with the remaining PCs and servers. Do not claim end-to-end connectivity until routing and ping tests have succeeded.
