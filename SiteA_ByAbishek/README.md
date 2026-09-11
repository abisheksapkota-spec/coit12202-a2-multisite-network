# Site A — owned by Abishek

![SiteA_topology](SiteA_Topology)

- Place the **Site A** GNS3 export (`.gns3project`) directly in this folder.
- `Addressing.md` — full addressing scheme for this site's actual build (two firewalls, transit segment, LAN, DMZ).
- `configs/` — exported OPNsense config, Kerberos config, and other device configuration notes.
- `captures/` — packet capture evidence.
- `Images/` — screenshots: network topology, OPNsense zones/interfaces, connectivity, Kerberos build.

**Addressing:** `10.1.0.0/16` (see `Addressing.md` for the full breakdown) · **Realm:** `SITEA.LOCAL`

## Status

- [x] Topology built and wired (two firewalls: OPNsense1 edge, OPNsense2 internal)
- [x] All hosts, VPNRouter1, IDS1 addressed
- [x] Kerberos realm renamed to `SITEA.LOCAL` on KDC/SERVER/CLIENT
- [ ] OPNsense1/OPNsense2 console configuration
- [ ] OPT1 firewall pass rule
- [ ] GUI access
- [ ] KDC database, principals, keytab, GSSAPI test
- [ ] Tailscale on VPNRouter1
- [ ] Site-to-site VPN (Phase 1 now, Phase 2 once a peer exists)
- [ ] Suricata ruleset on IDS1

See `Addressing.md` for the full device-by-device breakdown.
