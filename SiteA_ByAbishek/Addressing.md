# Site A - Addressing

Owner: Abishek - Realm: `SITEA.LOCAL` - Supernet: `10.1.0.0/16`

## Architecture

```
NAT1 - VPNRouter1 (Tailscale) - OPNsense1 (edge, VPN tunnel terminates here) - Switch2 - OPNsense2 (internal) --- Switch1 (LAN)
                                                                                                                --- Switch3 (DMZ) - Hub1 - IDS1
```

## Segments

| Segment | Subnet | Notes |
|---|---|---|
| Transit (WAN, advertised via Tailscale) | `10.1.9.0/24` | OPNsense1 WAN sits here |
| Inter-firewall | `10.1.8.0/24` | Internal only, not advertised |
| LAN | `10.1.10.0/24` | Kerberos hosts + Firefox host |
| DMZ | `10.1.20.0/24` | Web server, IDS tap |

## Devices

| Device | Interface | Address | Gateway |
|---|---|---|---|
| OPNsense1 | WAN | `10.1.9.1/24` | - |
| OPNsense1 | LAN | `10.1.8.1/24` | - |
| OPNsense2 | WAN | `10.1.8.2/24` | `10.1.8.1` |
| OPNsense2 | LAN | `10.1.10.1/24` | - |
| OPNsense2 | OPT1 (DMZ) | `10.1.20.1/24` | - |
| VPNRouter1 | transit | `10.1.9.2/24` | - |
| VPNRouter1 | internet (to NAT1) | DHCP | - |
| KDC | eth0 | `10.1.10.10/24` | `10.1.10.1` |
| SERVER | eth0 | `10.1.10.20/24` | `10.1.10.1` |
| CLIENT | eth0 | `10.1.10.30/24` | `10.1.10.1` |
| Host1 (Firefox) | eth0 | `10.1.10.40/24` | `10.1.10.1` |
| WEB-A | eth0 | `10.1.20.10/24` | `10.1.20.1` |
| Host5 | eth0 | `10.1.20.20/24` | `10.1.20.1` |
| Host6 | eth0 | `10.1.20.30/24` | `10.1.20.1` |
| IDS1 | eth0 | `10.1.20.40/24` | `10.1.20.1` |

## Tailscale overlay

Tunnel terminates on each site's OPNsense WAN address, per the unit lecture and reference slides - not the VPN router. Type: IPsec, IKEv2, policy-based, PSK.

| Site | Node name | Tailscale address | Route advertised | Route approved |
|---|---|---|---|---|
| Site A | vpnrouter1 | `100.106.102.86` | `10.1.0.0/16` | Yes |
| Site B | (fill in) | | `10.2.0.0/16` | |
| Site C | (fill in) | | `10.3.0.0/16` | |

## Status

- [x] Topology built and wired
- [x] All hosts, VPNRouter1, IDS1 addressed
- [x] Kerberos realm renamed to `SITEA.LOCAL` on KDC/SERVER/CLIENT
- [x] KDC database, principals, keytab, GSSAPI test (positive and negative proof)
- [x] Kerberos packet capture (6 packets due to a double kinit - see captures/README.md)
- [x] OPNsense1 interfaces + IP addressing + WAN firewall rule
- [x] OPNsense2 interfaces + IP addressing + WAN/OPT1 firewall rules
- [x] GUI access working (VNC bridge via Host1's Firefox)
- [x] Tailscale installed on VPNRouter1, device approved, route approved and confirmed live
- [ ] Site-to-site VPN Phase 1 on OPNsense1 (not OPNsense2 - see note below)
- [ ] Site-to-site VPN Phase 2 (needs a peer site)
- [ ] VPN before/after capture
- [ ] Suricata ruleset on IDS1

**Note:** the tunnel terminates on OPNsense1 (edge firewall), not OPNsense2 - per the unit lecture: "the tunnel runs firewall WAN to firewall WAN, inside the Tailscale network." OPNsense1 is the one with the WAN interface on the Tailscale-advertised transit segment.
