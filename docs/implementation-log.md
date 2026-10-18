# Implementation Log

## Checkpoint 1 — Device setup
- Inventory: two Cisco 2911 routers, two Cisco 2960-24TT switches, four PC-PT endpoints, and two Server-PT endpoints.

## Checkpoint 2 — Router interface addressing
| Device/interface | IP address | Subnet mask | Intended role |
|---|---|---|---|
| Router1 G0/0 | 192.168.10.1 | 255.255.255.0 | LAN 1 gateway |
| Router1 G0/1 | 10.0.0.1 | 255.255.255.252 | Router-to-router link |
| Router2 G0/0 | 10.0.0.2 | 255.255.255.252 | Router-to-router link |
| Router2 G0/1 | 192.168.20.1 | 255.255.255.0 | LAN 2 gateway |

The user reported that both used interfaces on each router were up/up.

## Checkpoint 3 — End-device addressing and routing
The following static addresses and default gateways were entered:

| Device | Address | Gateway |
|---|---|---|
| PC1 | 192.168.10.10/24 | 192.168.10.1 |
| PC2 | 192.168.10.11/24 | 192.168.10.1 |
| Server1 | 192.168.10.20/24 | 192.168.10.1 |
| PC3 | 192.168.20.10/24 | 192.168.20.1 |
| PC4 | 192.168.20.11/24 | 192.168.20.1 |
| Server2 | 192.168.20.20/24 | 192.168.20.1 |

Static routes:
- Router1: `ip route 192.168.20.0 255.255.255.0 10.0.0.2`
- Router2: `ip route 192.168.10.0 255.255.255.0 10.0.0.1`

The user reported successful router-to-router ping tests and successful cross-LAN ping tests before adding the ACL. The original ping output screenshots have not yet been added to the evidence folder.

## Checkpoint 4 — Router2 ACL security test
An extended ACL was configured on Router2 and applied outbound to GigabitEthernet0/1:

```text
ip access-list extended BLOCK_PC1_TO_SERVER2
 deny ip host 192.168.10.10 host 192.168.20.20
 permit ip any any
!
interface GigabitEthernet0/1
 ip access-group BLOCK_PC1_TO_SERVER2 out
```

### Verification
- `show access-lists` displayed the ACL with the deny and permit entries.
- `show ip interface GigabitEthernet0/1` reported the outgoing access list as `BLOCK_PC1_TO_SERVER2`.
- The user tested PC1 to Server2 and reported that the ping failed.
- The user tested PC2 to Server2 and reported that the ping succeeded.
- The subsequent ACL counters showed 5 matches on the deny entry and 1 match on the permit entry.

### Interpretation and limitation
The result demonstrates a host-specific router ACL policy: PC1 is blocked from reaching Server2, while the tested PC2 traffic is permitted. This is ACL-based access control, not proof of a full IDS/IPS deployment.

## Remaining work
- Add screenshots of the router-to-router test, static routing tables, ACL configuration/counters, and PC1/PC2 ping results to `evidence/`.
- Investigate whether the Packet Tracer IOS image/device supports an IOS IPS feature before claiming IDS/IPS implementation.
- If IOS IPS is unavailable, document that limitation and describe the ACL as the security control actually implemented.
