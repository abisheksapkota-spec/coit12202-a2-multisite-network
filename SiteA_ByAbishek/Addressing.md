# Site A - Addressing

Owner: Abishek · Realm: `SITEA.LOCAL` · Supernet: `10.1.0.0/16`

## Architecture

```
NAT1 — VPNRouter1 (Tailscale) -- OPNsense1 (edge, VPN tunnel terminates here) -- Switch2 -- OPNsense2 (internal) ─┬─ Switch1 (LAN)
                                                                                                                  └- Switch3 (DMZ) -- Hub1 -- IDS1
```

**Site-to-site IPsec/WireGuard tunnel terminates on OPNsense1's WAN**, not `VPNRouter1` - per the course lecture and reference slides: *"the tunnel runs firewall WAN to firewall WAN, inside the Tailscale network."* `VPNRouter1` handles the Tailscale underlay only.

## Segments

| Segment | Subnet | Notes |
|---|---|---|
| Transit (WAN, advertised via Tailscale) | `10.1.9.0/24` | `OPNsense1` WAN sits here - this is the actual VPN tunnel endpoint address once built |
| Inter-firewall | `10.1.8.0/24` | Internal only, not advertised - connects the two firewalls |
| LAN | `10.1.10.0/24` | Kerberos hosts + Firefox host |
| DMZ | `10.1.20.0/24` | Web server, IDS tap |

## Devices

| Device | Interface | Address | Gateway |
|---|---|---|---|
| OPNsense1 | WAN (vtnet0/em0) | `10.1.9.1/24` | - |
| OPNsense1 | LAN (vtnet1/em1) | `10.1.8.1/24` | - |
| OPNsense2 | WAN (vtnet0/em0) | `10.1.8.2/24` | `10.1.8.1` |
| OPNsense2 | LAN (vtnet1/em1) | `10.1.10.1/24` | - |
| OPNsense2 | OPT1 (vtnet2/em2) | `10.1.20.1/24` | - |
| VPNRouter1 | eth1 (transit) | `10.1.9.2/24` | - |
| VPNRouter1 | eth0 (to NAT1) | DHCP | - |
| KDC | eth0 | `10.1.10.10/24` | `10.1.10.1` |
| SERVER | eth0 | `10.1.10.20/24` | `10.1.10.1` |
| CLIENT | eth0 | `10.1.10.30/24` | `10.1.10.1` |
| Host1 (Firefox) | eth0 | `10.1.10.40/24` | `10.1.10.1` |
| WEB-A | eth0 | `10.1.20.10/24` | `10.1.20.1` |
| Host5 | eth0 | `10.1.20.20/24` | `10.1.20.1` |
| Host6 | eth0 | `10.1.20.30/24` | `10.1.20.1` |
| IDS1 | eth0 | `10.1.20.40/24` | `10.1.20.1` |

## Firewall rules in place

| Interface | Rule | Status |
|---|---|---|
| OPNsense1 WAN | Pass (any/any) | Done |
| OPNsense2 WAN | Block private/bogon disabled + Pass rule added | Done |
| OPNsense2 OPT1 | Pass rule added | Done |
<img width="940" height="695" alt="image" src="https://github.com/user-attachments/assets/c23f0af8-adc4-47e2-914e-ca2679ca945b" />


## Status

- [x] Topology built and wired
- [x] All hosts, VPNRouter1, IDS1 addressed
- [x] Kerberos realm renamed to `SITEA.LOCAL` on KDC/SERVER/CLIENT
- [x] OPNsense1 interfaces + IP addressing
- [x] OPNsense2 interfaces + IP addressing
- [x] OPNsense2 WAN + OPT1 firewall rules
- [x] GUI access working (VNC bridge via Host1's Firefox)
- [ ] OPNsense1: disable block-private/bogon on WAN
- [ ] Wire transit segment (OPNsense2 side already exists; VPNRouter1 ↔ Switch5 ↔ NAT1 still needed)
- [ ] Tailscale on VPNRouter1 (not yet installed/configured)
- [ ] Advertise `10.1.0.0/16` route + get it approved in Tailscale admin console
- [ ] KDC database, principals, keytab, GSSAPI test
- [ ] Site-to-site VPN - Phase 1 on OPNsense1 (Phase 2 needs a peer site)
- [ ] Suricata ruleset on IDS1
