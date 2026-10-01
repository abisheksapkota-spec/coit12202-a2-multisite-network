# Packet Capture Evidence (Consolidated)

This file indexes all packet-capture and connectivity-test evidence collected across the three sites. Each capture lives in its own site folder alongside its detailed write-up; this table is the single point of reference across all of them.

| File | Site | Captured at | Filter | Proves |
|---|---|---|---|---|
| [`Auth-Kerberos-12312491-client.pcap`](../SiteA_ByAbishek/captures/Auth-Kerberos-12312491-client.pcap) | Site A | `client`-`Switch1` link | `kerberos` | Full Kerberos AS-REQ/AS-REP/TGS-REQ/TGS-REP exchange for `abishek`, plus a negative-proof SSH attempt with zero Kerberos packets. See [`SiteA_ByAbishek/captures/README.md`](../SiteA_ByAbishek/captures/README.md). |
| [`Client_SiteA_to_SiteB.pcap`](../SiteA_ByAbishek/captures/Client_SiteA_to_SiteB.pcap) | Site A <-> Site B | `OPNsense1`-`VPNRouter1` link | `icmp` | Site A to Site B connectivity over the Tailscale-carried transit link - 5 request/reply pairs, 0% loss. See [`SiteA_ByAbishek/captures/README.md`](../SiteA_ByAbishek/captures/README.md). |
| [`SiteB-to-SiteC-Ping-Success.png`](../SiteB_ByAnima/captures/SiteB-to-SiteC-Ping-Success.png) | Site B <-> Site C | Site B client console | ICMP (`ping 10.3.9.2`) | Site B successfully reaching Site C - 5/5 packets, 0% loss. See [`SiteB_ByAnima/captures/README.md`](../SiteB_ByAnima/captures/README.md). |
| [`ClientCtoSiteA.pcap`](../SiteC_ByUmesh/ClientCtoSiteA.pcap) | Site C <-> Site A | Site C | - | Site C establishing the IPsec tunnel and sending encrypted traffic toward Site A. See [`SiteC_ByUmesh/README.md`](../SiteC_ByUmesh/README.md). |

Evidence of the Kerberos authentication and VPN connectivity that each capture above demonstrates is also summarised in [`report/report.md`](../report/report.md) and [`RUBRIC-CHECKLIST.md`](../RUBRIC-CHECKLIST.md).
