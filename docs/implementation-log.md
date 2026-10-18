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

## Checkpoint 5 — IOS IPS configuration attempt on Router1
- Router1 is a Cisco 2911 running IOS 15.1(4)M4. The user enabled the `securityk9` technology package under its evaluation license and rebooted the router; `show version` subsequently showed `securityk9` as the current package.
- The router's `flash0:/` directory listing included `sigdef-category.xml` and `sigdef-default.xml`.
- IPS signature categories were configured with `category ios_ips basic` and `retired false`; IOS reported the `atomic-ip` engine ready and scanning packets, initially with 288 signatures reported in the build message.
- The IPS rule `EDGE-IPS` was created and applied inbound on Router1 `GigabitEthernet0/0` using `ip ips EDGE-IPS in`. The running configuration confirms this interface command.
- `ip ips config location flash0:` was accepted syntactically, but IOS emitted `%IPS-3-IPS_FILE_OPEN_ERROR` for `sigdef-default.xml`, `sigdef-delta.xml`, and `sigdef-category.xml`, saying the directory does not exist. `show ip ips all` continues to report 0 active and 0 inactive signatures and blank signature load times. Its latest output also has an empty interface-status section, despite the running configuration showing `ip ips EDGE-IPS in`.
- After applying the interface rule, IOS emitted engine-build messages reporting `atomic-ip - 3 signatures` and `ENGINE_READY`. This build message does not override the separate status output showing zero active signatures.

### IPS test interpretation and limitation
- PC1 ping to Server2 (`192.168.20.20`) failed, consistent with the existing Router2 ACL `BLOCK_PC1_TO_SERVER2`.
- PC2 ping to Server2 received three replies and one packet loss in the reported test; repeat the test to confirm stable connectivity.
- These pings do not demonstrate IPS detection or blocking, because the ACL already explains PC1's failed ping and no IPS signature event has been verified. Treat IOS IPS as configured but not verified operationally in this Packet Tracer image until signature loading/status issues are resolved.

## Remaining work
- Resolve why IOS IPS cannot load signature files from `flash0:` in this Packet Tracer IOS image, or explicitly document this simulation limitation if no supported fix is available.
- Recheck `show ip ips all`, the running configuration, and `show logging` after the signature-loading issue is addressed.
- Add screenshots of the router-to-router test, static routing tables, ACL configuration/counters, and PC1/PC2 ping results to `evidence/`.
