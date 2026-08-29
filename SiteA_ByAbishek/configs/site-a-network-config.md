# Site A — Network Configuration

Owner: Abishek · Realm: `SITEA.LOCAL` · Supernet: `10.1.0.0/16`

## Interface assignment (OPNsense1)

| Role | Adapter | Wired to |
|---|---|---|
| WAN | vtnet0 | Cloud1 |
| LAN | vtnet1 | Switch1 (kdc, server, client) |
| OPT1 (DMZ) | vtnet2 | Switch2 (web-a) |
| OPT2 (MGMT) | vtnet3 | Switch3 (admin-a) |

## Zone addressing

| Zone | Interface | Subnet | Gateway |
|---|---|---|---|
| LAN | vtnet1 | `10.1.10.0/24` | `10.1.10.1` |
| DMZ | vtnet2 (OPT1) | `10.1.20.0/24` | `10.1.20.1` |
| MGMT | vtnet3 (OPT2) | `10.1.30.0/24` | `10.1.30.1` |
| WAN | vtnet0 | DHCP-assigned | — |

## Host addressing

| Host | Zone | Address | Notes |
|---|---|---|---|
| kdc | LAN | `10.1.10.10/24` | Kerberos KDC |
| server | LAN | `10.1.10.20/24` | Kerberos service host |
| client | LAN | `10.1.10.30/24` | Kerberos client |
| web-a | DMZ | `10.1.20.10/24` | Linux Server |
| admin-a | MGMT | `10.1.30.10/24` | Ubuntu Host, admin workstation |

## Firewall rules

| Interface | Rule | Status |
|---|---|---|
| LAN | default allow (built-in) | active |
| OPT1 (DMZ) | Pass ICMP, source OPT1 net, dest any | *(add screenshot once confirmed)* |
| OPT2 (MGMT) | Pass ICMP, source OPT2 net, dest any | *(add screenshot once confirmed)* |

**Note:** current OPT1/OPT2 rules are minimal (ICMP only) to prove connectivity. Before this counts as real evidence for the report, tighten/expand per what each zone actually needs to do (e.g. does MGMT need more than ping? does DMZ need inbound from WAN for the web service?) — document that reasoning in the report's Firewalls & Zones section.

## Connectivity verified

- [x] kdc → 10.1.10.1
- [x] server → 10.1.10.1
- [x] client → 10.1.10.1
- [x] web-a → 10.1.20.1
- [x] admin-a → 10.1.30.1
- [x] WAN → DHCP lease obtained (`192.168.56.x`)

## Known build notes

Initial interface assignment had WAN and LAN swapped (WAN was assigned to the adapter physically wired to Switch1, LAN to the adapter wired to Cloud1) — corrected via console option 1, reassigning WAN→vtnet0, LAN→vtnet1 to match actual wiring.
