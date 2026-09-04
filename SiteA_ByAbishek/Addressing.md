# Site A — Addressing

Owner: Abishek · Realm: `SITEA.LOCAL` · Supernet: `10.1.0.0/16`

## Topology

```
NAT1 — VPNRouter1 — OPNsense1 (edge) — Switch2 — OPNsense2 (internal) ─┬─ Switch1 (LAN)
                                                                         └─ Switch3 (DMZ) — Hub1
```

See `Images/SiteA-network-topology.png` for the full diagram.

## Segments

| Segment | Subnet | Gateway |
|---|---|---|
| WAN/transit (advertised via Tailscale) | `10.1.9.0/24` | `OPNsense1` em0: `10.1.9.1` |
| Inter-firewall | `10.1.8.0/24` | `OPNsense1` em1: `10.1.8.1` · `OPNsense2` em0: `10.1.8.2` |
| LAN | `10.1.10.0/24` | `OPNsense2` em1: `10.1.10.1` |
| DMZ | `10.1.20.0/24` | `OPNsense2` em2: `10.1.20.1` |

## Devices

| Device | Role | Zone | Address |
|---|---|---|---|
| Host1 | Firefox (GUI access) | LAN | `10.1.10.40` |
| KDC | Kerberos KDC | LAN | `10.1.10.10` |
| SERVER | Kerberos service host | LAN | `10.1.10.20` |
| CLIENT | Kerberos client | LAN | `10.1.10.30` |
| WEB-A | Public-facing service | DMZ | `10.1.20.10` |
| Host5 | DMZ host | DMZ | `10.1.20.20` |
| Host6 | DMZ host (via Hub1) | DMZ | `10.1.20.30` |
| IDS1 | Suricata IDS (via Hub1) | DMZ | `10.1.20.40` |
| VPNRouter1 | Tailscale | transit / internet | `10.1.9.2` (transit) · DHCP (internet side) |
| OPNsense1 | Edge firewall | WAN / inter-firewall | `10.1.9.1` (WAN) · `10.1.8.1` (LAN) |
| OPNsense2 | Internal firewall | inter-firewall / LAN / DMZ | `10.1.8.2` (WAN) · `10.1.10.1` (LAN) · `10.1.20.1` (OPT1) |

## Status

- [x] Topology built and wired
- [x] All hosts, VPNRouter1, IDS1 addressed
- [x] Kerberos realm renamed to `SITEA.LOCAL` on KDC/SERVER/CLIENT
- [ ] OPNsense1/OPNsense2 console configuration
- [ ] OPT1 firewall pass rule
- [ ] GUI access
- [ ] KDC database, principals, keytab, GSSAPI test
- [ ] Tailscale on VPNRouter1
- [ ] Site-to-site VPN (Phase 1 now, Phase 2 once a peer exists)
- [ ] Suricata ruleset on IDS1
