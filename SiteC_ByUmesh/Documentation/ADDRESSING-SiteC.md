# Site C — Addressing

Owner: Umesh · Realm: `SITEC.LOCAL` · Supernet: `10.3.0.0/16`

## Architecture

```
NAT-C — VPNRouter-C (Tailscale) — OPNsense-C (VPN tunnel terminates here) ─┬─ Switch-LAN-C
                                                                            └─ Switch-DMZ-C — Hub-C — IDS-C
```

**Site-to-site IPsec/WireGuard tunnel terminates on OPNsense-C's WAN** — per the course lecture and reference slides: *"the tunnel runs firewall WAN to firewall WAN, inside the Tailscale network."* `VPNRouter-C` handles the Tailscale underlay only, same split as Site A.

## Segments

| Segment | Subnet | Notes |
|---|---|---|
| Transit (WAN, advertised via Tailscale) | `10.3.9.0/24` | `OPNsense-C` WAN sits here — the actual VPN tunnel endpoint address once built |
| LAN | `10.3.10.0/24` | Kerberos hosts + Firefox host |
| DMZ | `10.3.20.0/24` | Web server, IDS tap |

## Devices

| Device | Interface | Address | Gateway |
|---|---|---|---|
| OPNsense-C | WAN | `10.3.9.1/24` | — |
| OPNsense-C | LAN | `10.3.10.1/24` | — |
| OPNsense-C | OPT1 (DMZ) | `10.3.20.1/24` | — |
| VPNRouter-C | eth1 (transit) | `10.3.9.2/24` | — |
| VPNRouter-C | eth0 (to NAT-C) | DHCP | — |
| kdc-C | eth0 | `10.3.10.10/24` | `10.3.10.1` |
| server-C | eth0 | `10.3.10.20/24` | `10.3.10.1` |
| client-C | eth0 | `10.3.10.30/24` | `10.3.10.1` |
| Host1-C (Firefox) | eth0 | `10.3.10.40/24` | `10.3.10.1` |
| web-C | eth0 | `10.3.20.10/24` | `10.3.20.1` |
| IDS-C | eth0 | `10.3.20.20/24` | `10.3.20.1` |

## Status

- [x] Topology built and wired (delivered as `SiteC-Umesh.gns3project`)
- [x] All hosts, VPNRouter-C addressed
- [x] Kerberos realm renamed to `SITEC.LOCAL` on kdc-C/server-C/client-C
- [ ] OPNsense-C: factory reset first (disk is a clone of a configured instance, not blank)
- [ ] OPNsense-C: assign interfaces (WAN/LAN/OPT1)
- [ ] OPNsense-C: set IP addressing
- [ ] OPNsense-C: disable block-private/bogon on WAN
- [ ] OPNsense-C: add WAN + OPT1 firewall pass rules
- [ ] Get OPNsense-C GUI access working
- [ ] Kerberos: initialize KDC database, principals, keytab, GSSAPI test
- [ ] Tailscale on VPNRouter-C: install, advertise `10.3.0.0/16`, get route approved
- [ ] Site-to-site VPN — Phase 1 on OPNsense-C (Phase 2 needs a peer site)
- [ ] Suricata ruleset on IDS-C
