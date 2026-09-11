# Site A - owned by Abishek

Realm: `SITEA.LOCAL` - Supernet: `10.1.0.0/16` - two firewalls (edge + internal)

## Network topology

![Site A network topology](Images/SiteA_Topology.png)

Full addressing is labelled directly on the diagram. See `Addressing.md` for the same information as a table.

## Status

- [x] Topology built and wired (two firewalls: OPNsense1 edge, OPNsense2 internal)
- [x] All hosts, VPNRouter1, IDS1 addressed
- [x] Kerberos realm renamed to `SITEA.LOCAL` on KDC/SERVER/CLIENT
- [x] OPNsense1 and OPNsense2 console configuration (interfaces, IP addressing)
- [x] OPNsense1 and OPNsense2 WAN and OPT1 firewall rules
- [x] GUI access (VNC bridge via Host1's Firefox)
- [x] KDC database, principals, keytab, GSSAPI test (positive and negative proof)
- [x] Kerberos packet capture confirmed (`captures/ClintToSwitchSiteA.pcap`)
- [x] Tailscale on VPNRouter1 - live, route approved, confirmed reaching Site B
- [ ] Site-to-site VPN Phase 1 (IKE) on OPNsense1
- [ ] Site-to-site VPN Phase 2 (needs Site B or C's tunnel endpoint)
- [ ] VPN before/after packet capture
- [ ] Suricata ruleset on IDS1
- [ ] `.gns3project` export added to this folder (still outstanding)

## Evidence

**Kerberos**

| | |
|---|---|
| ![KDC database init](Images/kerberos-02-kdc-database-init.png) | ![Principals and keytab](Images/kerberos-03-principals-keytab-listprincs.png) |
| KDC database initialised, both daemons running | All principals created, keytab exported |

![GSSAPI positive and negative proof](Images/kerberos-04-gssapi-positive-negative-proof.png)

Login succeeds with a valid ticket, fails once `kdestroy` removes it - proof the ticket, not something else, is what authenticated the login.

**OPNsense - diagnosing and fixing a real bug**

| Before | After |
|---|---|
| ![Ping fails](Images/opnsense1-to-opnsense2-ping-FAIL-before-fix.png) | ![Ping succeeds](Images/opnsense1-to-opnsense2-ping-SUCCESS-after-fix.png) |
| 100% packet loss - WAN had no Pass rule | 0% loss, after adding one |

Unticking "Block private networks" alone wasn't enough - an explicit WAN Pass rule was needed on top. See `Images/opnsense2-wan-rule-creation.png` for the rule itself.

**Tailscale**

![Tailscale connected](Images/tailscale-01-vpnrouter1-setup.png)

VPNRouter1 live on the shared tailnet, `10.1.0.0/16` advertised and approved.

## Folder contents

- `Addressing.md` - full addressing scheme, table form
- `configs/` - Kerberos build record, OPNsense config notes
- `captures/` - packet capture evidence
- `Images/` - all screenshots referenced above, plus additional connectivity/config evidence not shown inline here
