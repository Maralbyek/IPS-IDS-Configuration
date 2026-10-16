# Implementation Log

## Checkpoint 1 — Initial device setup

### Inventory
- 2 × Cisco 2911 routers
- 2 × Cisco 2960-24TT switches
- 4 × PC-PT endpoints
- 2 × Server-PT endpoints

### Changes made
- Issued `no shutdown` on GigabitEthernet0/0 and GigabitEthernet0/1 on Router 1.
- Issued `no shutdown` on GigabitEthernet0/0 and GigabitEthernet0/1 on Router 2.
- Router 2 was checked with `show ip interface brief`.

### Verified result
Router 2 reported GigabitEthernet0/0 and GigabitEthernet0/1 as `up/up`, with both IP addresses `unassigned`.

### Not yet verified or completed
- Router 1's full interface status output.
- Exact cable-to-port mapping across the topology.
- Router hostname changes (Router1 and Router2 were discussed, but the final prompts were not confirmed).
- IP address plan and interface addressing.
- Routing between the two LANs.
- PC/server addressing and gateway configuration.
- End-to-end ping and service tests.
- Any IDS/IPS or traffic-filtering implementation and results.
- Screenshot of the actual Packet Tracer workspace.

### Next checkpoint
Capture the current topology screenshot, confirm the device hostnames and cable/port mapping, then define and apply an IP addressing plan. Do not claim end-to-end connectivity until it has been tested.
