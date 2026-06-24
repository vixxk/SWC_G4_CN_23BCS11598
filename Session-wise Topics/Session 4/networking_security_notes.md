# Session 4 — Network Security & Core Concepts (Expanded)

These notes expand the earlier concise bullets with examples, CLI commands, protocol details, timers, and troubleshooting tips.

## ACL (Access Control List)
- Purpose: filter traffic by IP addresses, protocols, ports, interfaces, and sometimes applications.
- Implicit deny: any ACL that does not explicitly permit traffic will implicitly deny it at the end.

### Wildcard masks (Cisco basics)
- Wildcard mask is inverse of subnet mask: `0` = match bit, `1` = don't care bit.
- Example: `192.168.1.0 0.0.0.255` matches whole /24.

### Standard vs Extended
- Standard ACLs:
  - Match only source IP (and mask). Cannot filter by port or protocol.
  - Example (numbered): `access-list 10 permit 192.168.1.0 0.0.0.255`
  - Example (named):
    - `ip access-list standard STD_USERS` then `permit 192.168.1.0 0.0.0.255`
- Extended ACLs:
  - Match source, destination, protocol, and ports.
  - Example: `access-list 101 permit tcp any host 10.0.0.5 eq 80` (allow HTTP to host 10.0.0.5)
  - Named extended example:
    - `ip access-list extended WEB_ALLOW`
    - `permit tcp any host 10.0.0.5 eq 80`

### Ordering and performance
- ACLs are processed top-to-bottom; first match wins. Put specific rules before generic ones.
- Use logging sparingly (`log` or `log-input`) as it can affect performance; use for debugging then remove.

### Common CLI & verification (Cisco)
- Apply to interface: `ip access-group 101 in` (or `out`).
- Show: `show access-lists`, `show ip interface` (to see ACLs applied), `show logging`.

## Firewall types & features
- Stateless (packet-filter) firewall: checks headers only; faster but no connection awareness.
- Stateful firewall: tracks connections (TCP state) and allows return traffic without explicit rules.
- Next-Generation Firewall (NGFW): adds application awareness, URL filtering, intrusion prevention, TLS inspection.

### Common rules and debugging
- Allow established/related: many firewalls support `allow established` or dynamic return flows.
- Check counters/logs: `show firewall sessions`, firewall syslog, connection tables.

## NAT (Network Address Translation)
- Purpose: map private IP addresses to public addresses; hides internal topology.
- Important concepts: inside/outside interfaces, translation table, timeouts.

### Types with examples
- Static NAT (one-to-one): permanent mapping.
  - `ip nat inside source static 192.168.1.10 203.0.113.10`
- Dynamic NAT (pool): internal hosts use addresses from a public pool.
  - `ip nat pool P1 203.0.113.20 203.0.113.30 netmask 255.255.255.0`
  - Bind with ACL: `ip nat inside source list 1 pool P1`
- PAT / NAT overload (many-to-one using ports): typical home-router NAT.
  - `ip nat inside source list 1 interface GigabitEthernet0/0 overload`

### Verification
- `show ip nat translations` — current active translations.
- `show ip nat statistics` — counters and hits.

### NAT caveats
- Protocols that embed IP in payload (FTP, SIP, H.323) may need application-level helpers or ALG.
- Port forwarding: map a specific external port to an internal host/port (used for servers behind NAT).

## Congestion & Data Dropping
- Congestion happens when input rate exceeds link capacity or processing capabilities.

### Buffer management and drop policies
- Tail Drop: simplest — drop when buffer full. Can cause global synchronization.
- RED (Random Early Detection): proactively drop packets probabilistically before buffer full.
- WRED (Weighted RED): variant giving different drop probabilities per traffic class.

### Transport-level congestion control
- TCP reduces sending rate on loss/ECN; mechanisms include slow start, congestion avoidance, fast retransmit, and fast recovery.
- Algorithms: TCP Tahoe, Reno, NewReno, Cubic (Linux default), BBR (delay-based).

## STP (Spanning Tree Protocol) — details
- Purpose: eliminate switching loops by electing a single-rooted tree and blocking redundant links.

### Election and timers
- Root bridge: lowest bridge priority (default 32768) and if tied, lowest MAC.
- Bridge ID = `priority` + `system MAC` (priority in multiples of 4096 on some devices).
- BPDU timers: `hello` (default 2s), `max-age` (20s), `forward-delay` (15s).

### Port roles & states
- Port states: Disabled, Blocking, Listening, Learning, Forwarding.
- Port roles:
  - Root Port (RP): single port on non-root switch with lowest path cost toward root.
  - Designated Port (DP): port that forwards on a LAN segment (one per segment).
  - Non-designated (Blocking) ports: block to prevent loops.

### Path cost and selection
- Path cost depends on interface bandwidth (per IEEE tables). Root path cost is sum of link costs to root.
- To change root bridge, lower the priority on the desired root: `spanning-tree vlan 1 priority 4096`.

### Verification
- `show spanning-tree` shows root, path costs, roles, and port states.

## Ping & ICMP (expanded)
- `ping` sends ICMP Echo Request (Type 8) and expects Echo Reply (Type 0). Useful for reachability and RTT.
- ICMP messages (common types):
  - Echo Reply (0), Echo Request (8)
  - Destination Unreachable (3) — codes explain reason (net/host/protocol/port unreachable)
  - Time Exceeded (11) — TTL expired (used by traceroute)
  - Redirect (5), Source Quench (4, obsolete)

### Troubleshooting tips
- If `ping` fails, try `traceroute` (shows hop at which packets stop) and check ACLs/firewall on that hop.
- ICMP can be filtered by ACLs or firewalls — absence of echo reply doesn't always mean host down.

## IP header notes
- IPv4 header fields: Version, IHL, Type of Service/DSCP, Total Length, Identification, Flags, Fragment Offset, TTL, Protocol, Header Checksum, Source IP, Destination IP.
- Fragmentation: occurs when packet > MTU; DF (Don't Fragment) bit prevents fragmentation and can cause ICMP Fragmentation Needed.

## ARP & RARP
- ARP request: broadcast (ff:ff:ff:ff:ff:ff) asking "Who has IP X?"; owner replies with ARP reply (unicast).
- ARP cache: entries expire; `arp -a` lists entries on hosts.
- Gratuitous ARP: a host announces its IP/MAC (used for failover or updating caches).

## Troubleshooting checklist (concise)
- Connectivity: `ping`, then `traceroute`.
- Check ARP tables: `arp -a` / `show ip arp`.
- Check ACLs: `show access-lists`, `show ip interface`.
- Check NAT: `show ip nat translations` and `show ip nat statistics`.
- Check STP: `show spanning-tree`.

---
File: [Session 4/networking_security_notes.md](Session 4/networking_security_notes.md#L1)

If you want, I can now:
- add hands-on CLI lab steps for ACLs/NAT/STP, or
- generate diagrams (STP root election, NAT flow) or
- create printable slides from these notes.
Tell me which next.
