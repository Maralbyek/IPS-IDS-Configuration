# Evidence

This folder stores genuine screenshots captured from the Cisco Packet Tracer project. Screenshots should show actual topology, configuration, or test output. Do not include passwords or unrelated personal information.

## Existing evidence

- `01-topology.png` — overall Packet Tracer topology.
- `02-router1-interfaces.png` — Router1 interface status and IP addressing.
- `03-router2-interfaces.png` — Router2 interface status and IP addressing.

## Evidence still to capture and commit

- `04-router-connectivity.png` — successful Router1 ping to Router2 at `10.0.0.2` (the user reported that a repeat test achieved 5/5 replies).
- `05-router1-routing-table.png` — Router1 `show ip route`, showing the static route to `192.168.20.0/24` via `10.0.0.2`.
- `06-router2-routing-table.png` — Router2 `show ip route`, showing the static route to `192.168.10.0/24` via `10.0.0.1`.
- `07-acl-security-test.png` — Router2 `show access-lists` and ACL match counters.
- `08-pc1-blocked.png` — PC1 ping to Server2 (`192.168.20.20`) failing after the ACL was applied.
- `09-pc2-allowed.png` — PC2 ping to Server2 (`192.168.20.20`) succeeding after the ACL was applied.

The user reported the ping outcomes and supplied the ACL CLI output in chat. The screenshots above have not been uploaded to this repository yet. Update this list after the actual image files are committed.
