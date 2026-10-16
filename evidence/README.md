# Evidence

This folder stores genuine screenshots captured from the Cisco Packet Tracer project. Screenshots should show actual topology, configuration, or test output. Do not include passwords or unrelated personal information.

## Existing evidence

- `01-topology.png` — overall Packet Tracer topology.
- `02-router1-interfaces.png` — Router1 interface status and IP addressing.
- `03-router2-interfaces.png` — Router2 interface status and IP addressing.

## Evidence to add

Save each screenshot here after capturing the corresponding result:

- `04-router-connectivity.png` — successful Router1 ping to Router2 at `10.0.0.2` (the repeated test achieved 5/5 replies).
- `05-cross-network-ping.png` — successful ping between end devices on different LANs, for example PC1 to Server2.
- `06-router1-routing-table.png` — Router1 `show ip route`, showing the static route to `192.168.20.0/24` via `10.0.0.2`.
- `07-router2-routing-table.png` — Router2 `show ip route`, showing the static route to `192.168.10.0/24` via `10.0.0.1`.

Add further screenshots for security configurations and tests only after those controls have actually been configured and tested. Update this list when new screenshots are committed.
