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
An extended ACL was configured on Router2:

```text
ip access-list extended BLOCK_PC1_TO_SERVER2
 deny ip host 192.168.10.10 host 192.168.20.20
 permit ip any any
!
interface GigabitEthernet0/1
 ip access-group BLOCK_PC1_TO_SERVER2 out
```

### Verified test — 2026-10-25
The ACL was reattached to Router2 G0/1 with `ip access-group BLOCK_PC1_TO_SERVER2 out`, while the Router1 IPS rule remained detached.

- PC2 (`192.168.10.11`) pinged Server2 (`192.168.20.20`). The first run had 50% loss during initial requests; an immediate second run succeeded with 4/4 replies and 0% loss.
- PC1 (`192.168.10.10`) pinged Server2 and received `Destination host unreachable` responses from `10.0.0.2`, with 100% loss.
- Router2 `show access-lists` then displayed:
  - deny PC1-to-Server2: **4 matches**
  - permit any-to-any: **7 matches**

The deny counter incremented to four during the PC1 test, confirming that the ACL deny entry matched the blocked traffic. PC2's successful second ping confirms permitted traffic still passes.

### Current interface state
The ACL is **attached and verified active** on Router2 G0/1. Router1 IPS remains detached. No screenshots have yet been committed, so the test results are currently documented from user-provided CLI output rather than repository-stored image evidence.

## Checkpoint 5 — IOS IPS configuration attempt on Router1
- Router1 is a Cisco 2911 running IOS 15.1(4)M4. The user enabled the `securityk9` technology package under its evaluation license and rebooted the router; `show version` subsequently showed `securityk9` as the current package.
- The router's `flash0:/` directory listing included `sigdef-category.xml` and `sigdef-default.xml`.
- IPS signature categories were configured with `category ios_ips basic` and `retired false`; IOS reported the `atomic-ip` engine ready and scanning packets, initially with 288 signatures reported in the build message.
- The IPS rule `EDGE-IPS` was created and applied inbound on Router1 `GigabitEthernet0/0` using `ip ips EDGE-IPS in`. The running configuration confirmed this interface command before the latest troubleshooting.
- `ip ips config location flash0:` was accepted syntactically, but IOS emitted `%IPS-3-IPS_FILE_OPEN_ERROR` for the signature files, saying the directory does not exist. The original `flash0:/sigdef-category.xml` is 28,282 bytes; `sigdef-default.xml` and `sigdef-delta.xml` are each 227,537 bytes.
- The configured location was changed to `flash:ips5`. The directory listing `dir ips5` showed all three expected filenames, but the copied `sigdef-category.xml` was 0 bytes; the default and delta files were each 227,537 bytes. The original root-level category file remains intact.
- The configured location was restored to `flash:/` with `ip ips config location flash:/ retries 1`. `show ip ips all` reported `Total Active Signatures: 1`, `Total Inactive Signatures: 0`, and blank default/delta load times. It showed `EDGE-IPS` inbound on G0/0 before detachment.
- A later `show ip ips signatures count` reported SDF `S339.0`, one total signature, and an overall total of 1 enabled signature, but the per-engine `string-icmp` detail said 0 enabled and 1 retired. This conflicts with `show ip ips all` reporting 1 active signature. Do not infer that the signature is usable from these counters.

## Checkpoint 6 — Connectivity troubleshooting (2026-10-25)
The following results were reported during troubleshooting:
- PC2 address configuration: `192.168.10.11/24`, default gateway `192.168.10.1`.
- All visible cable indicators were green.
- Router1 ping to PC2 (`192.168.10.11`) succeeded.
- Router1 ping to Router2 transit address (`10.0.0.2`) succeeded.
- Router1 ping to Router2 LAN interface (`192.168.20.1`) succeeded.
- Router2 ping to Server2 (`192.168.20.20`) succeeded.
- Router2 ping to PC2 (`192.168.10.11`) succeeded.
- PC2 ping to Server2 timed out even after the Router2 ACL was detached from G0/1.
- The IPS rule was then detached from Router1 G0/0 using `no ip ips EDGE-IPS in`.
- After detaching the IPS rule, PC2 ping to Server2 succeeded with 4 replies and 0% loss.
- The Router2 ACL was subsequently reattached and tested independently while IPS remained detached. PC2-to-Server2 passed (4/4 replies on the second run), PC1-to-Server2 was blocked, and the deny counter showed four matches.

### Current security configuration status
- **Router2 ACL:** attached to G0/1 and verified by PC1/PC2 ping tests and ACL counters.
- **Router1 IPS rule:** temporarily detached from G0/0 for troubleshooting. The IPS definition and signature configuration have not been deleted. The earlier connectivity issue suggests the IPS interface attachment was implicated, but the exact mechanism is not established; signature status was already inconsistent.
- **Connectivity:** PC2-to-Server2 works with the ACL attached and IPS detached. PC1-to-Server2 is blocked by the ACL.

## Remaining work
1. Capture genuine screenshots of the topology, routing, ACL configuration/counters, and PC1-blocked / PC2-allowed ping results into `evidence/`.
2. Troubleshoot IOS IPS separately; do not reattach it to the tested traffic path until a controlled test is ready.
3. Investigate inconsistent `string-icmp` signature state without overwriting the intact root-level signature files with the zero-byte copy. If Packet Tracer cannot load a valid signature, document the limitation.
4. Do not claim IPS detection or blocking until valid signature status and an actual IPS event are verified.
