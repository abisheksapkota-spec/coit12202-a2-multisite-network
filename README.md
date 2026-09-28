# COIT12202 A2 - Secured Multi-Site Network

Secured multi-site network build (Kerberos, OPNsense, VPN, Suricata) - COIT12202 Assessment 2.
Team: **Abishek** (lead), **Anima**, **Umesh**.

> A distributed, three-site network built and secured end-to-end by our team - federated authentication, segmented firewalls, encrypted site-to-site connectivity, and intrusion detection, with every design decision justified in the accompanying report.

## Team

| Name | Role | Site | Realm |
|---|---|---|---|
| Abishek | Team leader | Site A | `SITEA.LOCAL` |
| Anima | Member | Site B | `SITEB.LOCAL` |
| Umesh | Member | Site C | `SITEC.LOCAL` |

## Site A network topology (as-built)

![Site A network topology](SiteA_ByAbishek/Images/SiteA_Topology.png)

Two firewalls (edge + internal), full addressing labelled on the diagram. Sites B and C follow a simpler single-firewall pattern, since a second firewall isn't required by the spec.

## Architecture overview

Three sites, each running an OPNsense firewall (Site A runs two), WAN/LAN/DMZ zones, a local Kerberos KDC, and Suricata - federated over a shared Tailscale overlay, with an encrypted site-to-site VPN mesh (IPsec) carrying the real inter-site traffic on top of it, terminating on each site's OPNsense WAN interface (not the VPN router), per the unit's own reference material.

![Architecture overview](docs/images/architecture-overview.png)

*(Full diagrams - overview and per-site zone detail - are also in [`A2-Team-Plan-Architecture.docx`](A2-Team-Plan-Architecture.docx).)*

## Repository structure

```
.
├── SiteA_ByAbishek/                # Abishek - GNS3 export, configs/, Images/, CA-HTTPS-Evidence/
├── SiteB_ByAnima/                  # Anima - GNS3 export, configs/, Images/
├── SiteC_ByUmesh/                  # Umesh - GNS3 export, configs/, SITE C IMAGES/
├── captures/                       # Shared .pcap/.pcapng evidence (VPN before/after, etc.)
├── report/
│   └── report.md                   # Security-justification report
├── docs/images/                    # Diagram exports used in this README
├── network-diagram.drawio          # Multi-site network diagram (starter - expand to match the final build)
├── ADDRESSING.md                   # IP/realm/VPN addressing scheme - source of truth
├── RUBRIC-CHECKLIST.md             # Marking rubric tracked as a checklist
├── A2-Team-Plan-Architecture.docx  # Architecture diagrams + task distribution
└── README.md
```

Each site folder has its own `README.md` explaining exactly what goes where.

## Key docs

- [`ADDRESSING.md`](ADDRESSING.md) - IP addressing, Kerberos realms, VPN tunnel subnets
- [`RUBRIC-CHECKLIST.md`](RUBRIC-CHECKLIST.md) - full marking rubric mapped to a checklist
- [`A2-Team-Plan-Architecture.docx`](A2-Team-Plan-Architecture.docx) - architecture diagrams and task distribution
- [`report/report.md`](report/report.md) - security-justification report *(in progress)*
- [`SiteA_ByAbishek/CA-HTTPS-Evidence/README.md`](SiteA_ByAbishek/CA-HTTPS-Evidence/README.md) - Site A's private CA + HTTPS evidence *(new, not yet mapped to a rubric line - see note there)*

## How the sites connect

1. **Tailscale overlay** - federates the three independently-hosted GNS3 topologies so they can reach each other over the real internet.
2. **Site-to-site VPN (IPsec/WireGuard)** - encrypted tunnels between each pair of OPNsense firewalls, carried over that Tailscale reachability.
3. **Cross-realm Kerberos** - each site runs its own KDC and realm; cross-realm trust lets a user at one site authenticate to resources at another.
4. **Suricata** - a consistent detection policy deployed at each site's LAN/WAN boundary.

## Planning

Tracked on our GitHub Project (Kanban board) - kept current throughout the build, not backfilled at the end. Day-to-day communication happens in our Microsoft Teams channel.

## Status

**Site A (Abishek)** - both firewalls configured and GUI access working, Kerberos proven end-to-end (positive and negative GSSAPI login, packet capture confirmed), Tailscale live and confirmed reaching Site B, Suricata running with both custom rules firing. Site-to-site IPsec Phase 1 and Phase 2 configured for both peers (Site B and Site C); the tunnel to Site C is now confirmed up from Site C's side (IKE_SA and CHILD_SA established, ESP traffic captured - see Site C's notes below), so re-verify and capture the same evidence from Site A's own console next. `.gns3project` export still needs adding to the repo. New this week: a private root/intermediate CA was built and used to issue a certificate for `www.sitea.local`; nginx serves it correctly and a separate client verifies the full chain (`openssl verify` -> OK, `curl --cacert` returns the page) without disabling certificate checks - see [`SiteA_ByAbishek/CA-HTTPS-Evidence/`](SiteA_ByAbishek/CA-HTTPS-Evidence/README.md).

**Site B (Anima)** - OPNsense-B addressed and configured. Kerberos built past where Site A had reached at the equivalent point - KDC, principals, keytab, and an actual client ticket already obtained. IDS-B has both a management address and routing configured. Tailscale connected to the shared tailnet. Not yet confirmed: a full GSSAPI login test (positive and negative, the way Site A proved it), VPN tunnel status, Suricata.

**Site C (Umesh)** - full topology built and wired, with a `.gns3project` export now committed to the repo. OPNsense-C fully configured (WAN rules scoped correctly to Site A's and Site B's transit ranges, LAN/OPT1 rules, DMZ connectivity proven). Kerberos is now complete locally: KDC database, principals, and keytabs are in place, and both `kinit`/`klist` (client) and keytab-based auth (server) have been demonstrated. Tailscale is configured to advertise `10.3.0.0/16`, and the subnet route is confirmed **approved** in the admin console (screenshot verified - this corrects the earlier "still pending" status). Site-to-site IPsec to Site A is confirmed up: IKE_SA and CHILD_SA established, and real ESP traffic between `10.3.9.1` and `10.1.9.1` was captured live. IPsec to Site B has Phase 1 + Phase 2 configured but not yet confirmed established. Suricata is running with a custom rule firing on ICMP. Remaining: bidirectional LAN-to-LAN traffic over the Site A tunnel, Site C <-> Site B connectivity, cross-realm Kerberos (a group task), and the required cross-site packet captures.

Detailed, up-to-date status lives in each site's own `README.md`.

🚧 In progress - due **Thursday, 1 October 2026, 11:45 PM**.
