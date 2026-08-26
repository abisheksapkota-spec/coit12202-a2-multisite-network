# Network addressing scheme

Agreed by the group before individual builds started. Update the owner column and commit this file to the repo root — every other document (report, diagrams, configs) should match what's here rather than each site's original individual-lab addressing.

## Site allocation

| Site | Owner | Supernet |
|---|---|---|
| Site A | *Abishek Sapkota* | `10.1.0.0/16` |
| Site B | *Anima* | `10.2.0.0/16` |
| Site C | *Umesh* | `10.3.0.0/16` |

## Per-site zones (OPNsense)

Interface order follows the Week 6 lab convention: `em0` = WAN, `em1` = LAN, `em2` (`OPT1`) = DMZ, `em3` (`OPT2`) = MGMT. Gateway is always `.1` in each subnet, same as the individual lab.

| Zone | Interface | Site A | Site B | Site C |
|---|---|---|---|---|
| WAN (uplink / Tailscale) | em0 | *(Tailscale-assigned)* | *(Tailscale-assigned)* | *(Tailscale-assigned)* |
| LAN | em1 | `10.1.10.0/24` | `10.2.10.0/24` | `10.3.10.0/24` |
| DMZ | em2 (OPT1) | `10.1.20.0/24` | `10.2.20.0/24` | `10.3.20.0/24` |
| MGMT | em3 (OPT2) | `10.1.30.0/24` | `10.2.30.0/24` | `10.3.30.0/24` |

## Kerberos hosts (inside each site's LAN)

Same relative layout as the Week 5 lab (`.10` / `.20` / `.30`), moved from the standalone `10.10.1.0/24` into each site's real LAN block. Realm names are site-scoped so cross-realm trust has something distinct to trust.

| Site | Realm | KDC | Server | Client |
|---|---|---|---|---|
| Site A | `SITEA.LOCAL` | `kdc.sitea.local` — `10.1.10.10` | `server.sitea.local` — `10.1.10.20` | `client.sitea.local` — `10.1.10.30` |
| Site B | `SITEB.LOCAL` | `kdc.siteb.local` — `10.2.10.10` | `server.siteb.local` — `10.2.10.20` | `client.siteb.local` — `10.2.10.30` |
| Site C | `SITEC.LOCAL` | `kdc.sitec.local` — `10.3.10.10` | `server.sitec.local` — `10.3.10.20` | `client.sitec.local` — `10.3.10.30` |

## Site-to-site VPN tunnel addressing

Full mesh — three tunnels. Reserved out of a range not used anywhere else, so tunnel traffic is never ambiguous with LAN/DMZ/MGMT traffic.

| Tunnel | Subnet | Site X | Site Y |
|---|---|---|---|
| A ↔ B | `10.99.0.0/30` | `10.99.0.1` (A) | `10.99.0.2` (B) |
| A ↔ C | `10.99.0.4/30` | `10.99.0.5` (A) | `10.99.0.6` (C) |
| B ↔ C | `10.99.0.8/30` | `10.99.0.9` (B) | `10.99.0.10` (C) |

*(WireGuard: this is the `wgX` interface address on each side. Route-based IPsec with a VTI: same idea. Policy-based IPsec: no interface address needed — delete this table's relevance and note it under Assumptions instead.)*

## Tailscale overlay

Each site's OPNsense (or a small dedicated services VM, if the package route doesn't work out) joins one shared tailnet. Tailscale assigns its own `100.x.x.x` addresses automatically — nothing to allocate here, just record each site's Tailscale node name once joined:

| Site | Tailscale node name |
|---|---|
| Site A | *(fill in)* |
| Site B | *(fill in)* |
| Site C | *(fill in)* |

## Assumptions

*(Document only what your work actually depends on — e.g. "WAN represents each site's path to the internet via the host machine's NAT, not a real ISP circuit"; "VPN tunnel type: IPsec / WireGuard — decided [date]"; "MGMT zone hosts: OPNsense admin, Suricata management interface, nothing else.")*
