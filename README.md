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
├── SiteA_ByAbishek/                # Abishek - GNS3 export, configs/, Images/
├── SiteB_ByAnima/                  # Anima - GNS3 export, configs/, Images/
├── SiteC_ByUmesh/                  # Umesh - GNS3 export, configs/, Images/
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

## How the sites connect

1. **Tailscale overlay** - federates the three independently-hosted GNS3 topologies so they can reach each other over the real internet.
2. **Site-to-site VPN (IPsec/WireGuard)** - encrypted tunnels between each pair of OPNsense firewalls, carried over that Tailscale reachability.
3. **Cross-realm Kerberos** - each site runs its own KDC and realm; cross-realm trust lets a user at one site authenticate to resources at another.
4. **Suricata** - a consistent detection policy deployed at each site's LAN/WAN boundary.

## Planning

Tracked on our GitHub Project (Kanban board) - link it here once created. Day-to-day communication happens in our Microsoft Teams channel.

## Status

Site A (Abishek) - fully built and verified: both firewalls configured, GUI access working, Kerberos proven end-to-end (positive and negative GSSAPI login, packet capture confirmed), Tailscale live and confirmed reaching Site B's VPN router. Remaining on Site A: the site-to-site IPsec tunnel itself, and Suricata.

Site B (Anima) - GNS3 project delivered and building has started; her VPN router is on the shared tailnet and reachable from Site A.

Site C (Umesh) - GNS3 project delivered, build not yet started.

Detailed, up-to-date status lives in each site's own `README.md`.

🚧 In progress - due **Thursday, 1 October 2026, 11:45 PM**.
