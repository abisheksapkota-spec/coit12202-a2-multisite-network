# Network addressing scheme

## Site allocation

| Site | Owner | Supernet |
|---|---|---|
| Site A | Abishek Sapkota | `10.1.0.0/16` |
| Site B | Anima | `10.2.0.0/16` |
| Site C | Umesh | `10.3.0.0/16` |

Site A runs two firewalls (edge + internal). Sites B and C run one each.

## Zones

| Zone | Site A | Site B | Site C |
|---|---|---|---|
| WAN / transit | `10.1.9.0/24` | `10.2.9.0/24` | `10.3.9.0/24` |
| Inter-firewall (Site A only) | `10.1.8.0/24` | n/a | n/a |
| LAN | `10.1.10.0/24` | `10.2.10.0/24` | `10.3.10.0/24` |
| DMZ | `10.1.20.0/24` | `10.2.20.0/24` | `10.3.20.0/24` |

No MGMT zone in any current build.

## OPNsense addressing

| Role | Site A OPNsense1 | Site A OPNsense2 | Site B/C OPNsense |
|---|---|---|---|
| WAN | `10.1.9.1/24` | n/a | `10.X.9.1/24` |
| LAN | `10.1.8.1/24` | `10.1.10.1/24` | `10.X.10.1/24` |
| OPT1 (DMZ) | n/a | `10.1.20.1/24` | `10.X.20.1/24` |

## Kerberos hosts

| Site | Realm | KDC | Server | Client |
|---|---|---|---|---|
| Site A | `SITEA.LOCAL` | `10.1.10.10` | `10.1.10.20` | `10.1.10.30` |
| Site B | `SITEB.LOCAL` | `10.2.10.10` | `10.2.10.20` | `10.2.10.30` |
| Site C | `SITEC.LOCAL` | `10.3.10.10` | `10.3.10.20` | `10.3.10.30` |

## Site-to-site VPN

Tunnel terminates on each site's OPNsense WAN address, per the lecture and reference slides. Type: IPsec, IKEv2, policy-based, PSK. No tunnel-interface addressing needed. PSKs shared via Teams, not the repo.

## Tailscale

Each site advertises its full `/16`, not just LAN. Advertised routes need manual approval in the Tailscale admin console.

| Site | Node name | Route advertised |
|---|---|---|
| Site A | - | `10.1.0.0/16` |
| Site B | - | `10.2.0.0/16` |
| Site C | - | `10.3.0.0/16` |
