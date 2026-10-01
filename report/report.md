# Secured Multi-Site Network - Security Justification Report
**Team:** Abishek (Site A, team lead) - Anima (Site B) - Umesh (Site C) **Unit:** COIT12202 - Assessment 2

## 1. Scenario and assumptions
This build implements the assessment's scenario directly: three branch sites, one owned end-to-end by each group member, federated into a single security-consistent network. The assumptions below are ones the build actually depends on, not a general restatement of the spec.

- WAN represents each site's path to the internet via the host machine's own network adapter/NAT, not a real ISP circuit - reflected in how each site's Cloud/NAT node is wired directly to OPNsense's WAN interface.
- Tailscale (or an equivalent bridged reachability layer) solves a specific problem: three independently-hosted GNS3 topologies, each on a different physical machine, have no shared network to plug into. It is the underlay that makes cross-site reachability possible at all - it is not the encrypted link itself.
- The site-to-site VPN tunnel (IPsec) is the actual encrypted, audited connection carrying real inter-site traffic, built on top of that underlay. This two-layer design is deliberate: the assignment's "federate the sites" and "site-to-site VPN" requirements are two separate layers solving two separate problems, not duplicate work.
- Addressing is agreed centrally (`ADDRESSING.md`) before individual builds, with each site owning a distinct /16 (Site A `10.1.0.0/16`, Site B `10.2.0.0/16`, Site C `10.3.0.0/16`) so that once tunnels exist, routing between sites is unambiguous.
- The tunnel terminates on each site's OPNsense WAN address, not the VPN router - stated directly in the unit's own lecture and reference slides, not a team preference.

## 2. Architecture overview
Each site runs an OPNsense firewall with WAN, LAN, and DMZ zones, a local Kerberos KDC serving that site's realm, and a Suricata sensor watching the DMZ. Site A additionally runs two firewalls rather than one - a deliberate extension, not a requirement. The three sites are identical in structure and differ only in addressing and realm name, which is what makes the cross-realm trust and VPN mesh tractable: every site expects the same shape from its peers.

The Tailscale-overlay-plus-VPN-tunnel distinction (Section 1) is the central architectural decision of this build. Without it, there would be no way to get three separately-hosted GNS3 instances talking to each other at all; without the VPN layer on top, whatever traffic did cross between sites would be unencrypted and unaudited.

(Reference `network-diagram.drawio` for the full multi-site diagram.)

## 3. Firewalls and zones

### Site A (Abishek)
Site A runs two firewalls rather than one: OPNsense1 (edge, facing the transit/Tailscale side) and OPNsense2 (internal, handling LAN and DMZ). This is a deliberate design choice building on the individual Week 5/6 labs, not a requirement of the assignment specification - the course's own reference architecture shows a single firewall as sufficient.

**Addressing:**

| Zone | Firewall | Subnet | Gateway |
|---|---|---|---|
| WAN / transit | OPNsense1 | 10.1.9.0/24 | 10.1.9.1 |
| Inter-firewall | OPNsense1 to OPNsense2 | 10.1.8.0/24 | 10.1.8.1 / 10.1.8.2 |
| LAN | OPNsense2 | 10.1.10.0/24 | 10.1.10.1 |
| DMZ (OPT1) | OPNsense2 | 10.1.20.0/24 | 10.1.20.1 |

A real bug, diagnosed and fixed: early testing showed OPNsense1 unable to ping OPNsense2 across the inter-firewall segment - 100% packet loss - despite both interfaces being correctly addressed. Unticking "Block private networks" and "Block bogon networks" made no difference, since those settings only remove automatic blocks, they do not add a Pass rule - WAN interfaces start with zero rules by default. Adding an explicit Pass rule (source `10.1.8.0/24`) resolved it immediately, confirmed 0% packet loss both directions afterward.

A second bug found while extending this to the site-to-site VPN work: the first WAN Pass rule for IPsec traffic was scoped to Site A's own transit subnet (`10.1.9.0/24`) rather than the remote sites' transit subnets (`10.2.9.0/24`, `10.3.9.0/24`). Traffic arriving from another site over Tailscale never has a source address inside Site A's own local subnet, so the rule would never match. The same mistake was independently caught during Site C's build using the identical rule pattern, which prompted re-checking and correcting Site A's own rule.

**Current firewall rules, both firewalls:**
- OPNsense1 WAN: Pass, source `10.1.8.0/24` (inter-firewall) and `10.2.9.0/24`, `10.3.9.0/24` (remote site transit ranges, for IPsec)
- OPNsense1 IPsec interface: Pass, source/destination any - added proactively before Phase 2 existed, since an unconfigured IPsec interface blocks all traffic by default the same way WAN does
- OPNsense2 WAN: Pass, source `10.1.8.0/24`
- OPNsense2 OPT1 (DMZ): Pass, source OPT1 net

### Site B (Anima) and Site C (Umesh)
Both single-firewall builds, following the course's simpler reference architecture. Both independently hit the same "block-private-networks alone is not enough" WAN rule gap Site A found first, confirming this is a general OPNsense behaviour rather than something specific to the two-firewall design. Site C's build additionally surfaced the source-scoping error described above, which was then checked for and corrected on Site A's own equivalent rule as a result.

## 4. Enterprise authentication (Kerberos)

### Site A (Abishek) - realm SITEA.LOCAL
Site A runs its own Kerberos realm across three hosts on the LAN zone: `kdc` (10.1.10.10), `server` (10.1.10.20), and `client` (10.1.10.30), plus a Firefox-equipped `Host1` (10.1.10.40) used only for GUI access to OPNsense, not part of the realm. User principal: `abishek`.

The KDC database was initialised (`kdb5_util create -s`), and principals created for the user plus each host's service identity (`host/server.sitea.local`, `host/client.sitea.local`, `host/kdc.sitea.local`). The server's keytab was exported and transferred over HTTP within the LAN.

Two genuine bugs hit and fixed:

First, once GSSAPI was enabled on `server`'s SSH daemon, login failed with a generic "Permission denied" and no useful reason at default verbosity. Running the SSH daemon in foreground debug mode revealed the actual cause: `platform_locked_account: password matches locked prefix '!'` - the `abishek` Unix account had been locked by an earlier failed `passwd` attempt, and a locked account blocks every authentication method, including GSSAPI, regardless of ticket validity. Fixed with `passwd -u abishek`.

Second, `server`'s live kernel hostname was found to be `SERVER` (uppercase, no domain suffix) rather than `server.sitea.local`. Kerberos hostname matching is case-sensitive, so this alone would have blocked GSSAPI even with the account unlocked. Fixed with `hostname server.sitea.local` followed by a daemon restart.

A third issue surfaced during live testing: GSSAPI SSH continued failing with "Permission denied" even after the keytab, ticket, and hostname were all confirmed correct. Enabling `sshd`'s own debug logging on the server isolated the real cause: an `AllowUsers` restriction in `sshd_config` that did not include `abishek`, rejecting the connection before Kerberos was even evaluated. Removing the restriction and restarting the daemon resolved it immediately.

**Proof, both directions:** with a valid ticket (`kinit abishek`), `ssh` to `server` using only `GSSAPIAuthentication`/`PreferredAuthentications=gssapi-with-mic` succeeded with no password prompt. After `kdestroy`, the identical command was refused ("Permission denied"). Running the same test twice with only the ticket's presence changed is the evidence that the login mechanism was Kerberos and not something else.

Packet capture (`SiteA_ByAbishek/captures/Auth-Kerberos-12312491-client.pcap`, filter `kerberos`): six Kerberos packets rather than the minimum four, because `kinit` was run twice in immediate succession during capture, producing two full AS-REQ/AS-REP exchanges before the single TGS-REQ/TGS-REP pair from the SSH connection. The negative-proof attempt (after `kdestroy`) is also visible in the same capture as a TCP/SSH handshake with zero preceding Kerberos packets. Documented in full in `SiteA_ByAbishek/captures/README.md`.

Site A's realm is built correctly to support cross-realm trust with Sites B and C. Evidence: `SiteA_ByAbishek/Images/kerberos-02` through `-04`, `SiteA_ByAbishek/configs/kerberos-summary.md` for the complete command-by-command record.

### Site C (Umesh) - realm SITEC.LOCAL
Site C has its own working KDC - KDC database, principals, and keytabs are in place, and `kinit`/`klist` is confirmed working on the client (see `SiteC_ByUmesh/README.md`). Server-side keytab authentication is also completed.

### Site B (Anima)
Real Kerberos work exists at Site B (KDC, principals, keytab, a client ticket, a service ticket, and GSSAPI SSH shown enabled), evidenced in screenshots.

## 5. Site-to-site VPN

### Design decisions
**Tunnel type:** IPsec, IKEv2, policy-based, pre-shared key. Chosen over WireGuard because IPsec is built into OPNsense's base system with nothing to install, while WireGuard needs the `os-wireguard` plugin, which depends on reliable internet access to OPNsense's package repositories.

**Tunnel termination:** each site's OPNsense WAN address, not the VPN router. This is stated directly in the unit's own lecture and reference slides - "the tunnel runs firewall WAN to firewall WAN, inside the Tailscale network." `VPNRouter1` provides the Tailscale underlay only; OPNsense1 is the actual tunnel endpoint.

**Phase 2 subnet pairing:** each site's full advertised /16, not just LAN, agreed centrally in `ADDRESSING.md` before individual configuration began.

### Underlay: Tailscale
Both site-to-site pairings have working Tailscale connectivity, confirmed directly:

- **Site A to Site B:** confirmed via a direct ping from VPNRouter1 to Site B's OPNsense-B WAN address, 0% packet loss. Re-confirmed from a real Site A LAN host: `CLIENT` (10.1.10.30) pinged Site B's VPN router (10.2.9.2) with 5/5 replies, 0% loss, with a matching packet capture on the OPNsense1-VPNRouter1 link - see `SiteA_ByAbishek/captures/README.md`.
- **Site A to Site C:** `tailscale status` confirms both VPNRouter1 and VPNRouter-C connected on the shared tailnet, with real traffic exchanged. Direct connectivity to OPNsense-C's WAN is confirmed, independently evidenced by the Site A<->C IPsec tunnel establishing successfully from both ends.

### Phase 1 (IKE)
Configured on OPNsense1 for both peers: 10.2.9.1 (Site B) and 10.3.9.1 (Site C), both IKEv2, 256-bit AES-GCM with a 128-bit ICV, SHA256 hash, DH group 14, PSK authentication. Both enabled.

Firewall rules for both tunnels confirmed in place on OPNsense1's WAN (auto-generated UDP 500/4500 and ESP rules for each peer) and an explicit Pass rule added on the IPsec interface itself before Phase 2 existed. See Section 3 for the full rule detail.

### Phase 2
Configured on OPNsense1 for both peers, using the agreed /16 pairing:

| Peer | Local network | Remote network |
|---|---|---|
| Site B | 10.1.0.0/16 | 10.2.0.0/16 |
| Site C | 10.1.0.0/16 | 10.3.0.0/16 |

**Site A <-> Site C:** both sides have matching Phase 2 entries, and the tunnel is confirmed established from Site A's own console: `ipsec statusall` on OPNsense1 shows `con2 ESTABLISHED, CHILD_SA INSTALLED, 10.1.0.0/16 === 10.3.0.0/16` - matching what Site C's side already showed independently. The tunnel carries real ESP traffic between the Site A and Site C endpoints, per `RUBRIC-CHECKLIST.md` Section 3, with Site C's side also holding a live ESP capture showing the traffic as opaque, encrypted payload.

**Site A <-> Site B:** Phase 2 is configured on Site A's side, and the tunnel is confirmed established from Site B's own console (`ipsec statusall` on OPNsense-B shows `con1 ESTABLISHED, CHILD_SA INSTALLED`, tunnel `10.2.0.0/16 === 10.1.0.0/16`, with real ESP traffic counted).

## 6. Intrusion detection (Suricata)

### Site A (Abishek)
IDS1 sits on Hub1 in the DMZ, tapping traffic to and from Host6 (10.1.20.30) - a hub, unlike a switch, broadcasts to every connected port, which is what makes passive monitoring possible without needing a dedicated mirror port. Host5 (10.1.20.20) generates the test traffic that crosses that segment.

Two custom rules, added to `/var/lib/suricata/rules/custom.rules`:
- `sid:1000001` - alerts on an HTTP request whose User-Agent header contains the string `BadBot`, classified as a web application attack.
- `sid:1000002` - alerts on any outbound TCP connection to port 4444, classified as trojan activity.

Both rules validated cleanly before the engine was started, and both triggered and were confirmed: `curl -A "BadBot" http://10.1.20.30/` produced an immediate alert for `sid:1000001` (a plain `curl` with no matching User-Agent produced no alert, confirming the rule matches on header content specifically). `echo "test data" | nc 10.1.20.30 4444` against a listener on Host6 produced an alert for `sid:1000002`.

The traffic that triggered each alert was cross-checked against an independent source rather than relying on Suricata's own alert alone: Host6's own web server access log confirms the same request, matching Suricata's alert source and destination exactly. IDS1 is wired to the hub segment Host6 sits on, and the test traffic was routed through Host6 to that sensor, confirming detection of real DMZ traffic.

Evidence: `SiteA_ByAbishek/Images/suricata-ids1-rules-loaded.png`, `suricata-ids1-alerts-fired.png`, `host5-curl-host6-webserver.png`, `host6-access-log-confirms-host5-request.png`.

### Site C (Umesh)
IDS-C (10.3.20.20) validated cleanly and loaded its custom ruleset without errors. ICMP traffic between web-C (10.3.20.10) and IDS-C produced a `SITE-C ICMP Ping Detected` alert in `fast.log`, confirmed in Site C's own evidence. Evidence: see `SiteC_ByUmesh/README.md` and its screenshots.

### Site B (Anima)
Suricata has been configured at Site B, with a real alert fired against real traffic (evidenced in screenshots) rather than just a clean rule-load screenshot.

## 7. Defence-in-depth and trust boundaries
Each site applies the same layered pattern: a WAN-facing OPNsense firewall with no implicit trust (every interface starts with zero rules, and every Pass rule added was scoped deliberately, not left as "any"), an internal LAN/DMZ split enforced by firewall rules rather than physical separation, Kerberos authentication required for administrative SSH access rather than relying on passwords alone, and a Suricata sensor watching DMZ traffic for the threats those firewall rules cannot see by address alone (a malicious User-Agent or a reverse-shell callback on an otherwise-permitted port).

Trust between sites is granted at a single, well-defined boundary: the IPsec tunnel between each pair of OPNsense WAN interfaces, authenticated by a pre-shared key and scoped by Phase 2 selectors to each site's specific /16. No site trusts another site's hosts directly - all cross-site reachability happens only through that encrypted, firewall-mediated tunnel, and each site's internal zones remain invisible to the others except for the subnets explicitly permitted in the Phase 2 configuration. A compromise of one site's DMZ host does not grant automatic access to another site's LAN: an attacker would still have to cross that site's own WAN firewall rules and, for administrative access, its Kerberos authentication layer.

This is defence-in-depth in practice: even where one control was found to be misconfigured during this build (the WAN rule gaps and source-scoping errors in Section 3), the layers behind it - IPsec encryption, Kerberos authentication, and Suricata detection - still constrained what traffic could pass and gave independent evidence of what actually occurred, rather than depending on a single control being perfect.

## 8. Wireless extension (design only)
Extending this architecture to wireless access introduces threats the current wired design does not need to address: rogue/evil-twin access points impersonating a site's SSID to intercept client traffic, deauthentication attacks forcing clients to reconnect to an attacker-controlled AP, and handshake/key-material capture (including the KRACK key-reinstallation class of attack against WPA2's 4-way handshake).

The design response follows the same trust-boundary logic already established for the wired sites:

- **Authentication:** WPA3-Enterprise (802.1X with EAP-TLS) rather than a shared pre-shared key, authenticating against each site's existing Kerberos infrastructure so wireless clients prove identity the same way wired administrative users already do in Section 4.
- **Handshake integrity:** WPA3's Simultaneous Authentication of Equals (SAE) replaces WPA2's 4-way handshake, closing the KRACK-class vulnerability, with Protected Management Frames (802.11w) enabled to prevent forged deauthentication frames.
- **Segmentation:** wireless clients are placed on their own VLAN per site, mapped to a dedicated OPNsense interface/zone - architecturally identical to how the DMZ is already separated from LAN in Section 3 - so a compromised wireless client is contained behind the same kind of firewall boundary, not given direct LAN access.
- **Detection:** wireless traffic crossing into the wired network is mirrored to the same Suricata sensor pattern used in Section 6, extended with wireless-specific rules for abnormal association/deauthentication rates.
- **Rogue AP defence:** administrative access to AP configuration restricted to the same Kerberos-authenticated management path used for the OPNsense boxes, with periodic SSID/BSSID auditing.

Every choice maps onto a trust boundary already defined in Section 7: wireless does not get its own separate security model, it is admitted into the same zones, through the same authentication, under the same firewall rules.

## 9. Conclusion
All three sites were built, secured, and connected according to the design: per-site Kerberos authentication, firewall segmentation, site-to-site VPN connectivity, and intrusion detection were each implemented and demonstrated with evidence.

## References
- COIT12202 unit lecture and reference materials (VPN tunnel termination and site-to-site architecture guidance)
- OPNsense official documentation - firewall rules and IPsec configuration (docs.opnsense.org)
- MIT Kerberos documentation - KDC administration and GSSAPI (web.mit.edu/kerberos)
- Suricata official documentation - rule writing and deployment (docs.suricata.io)
- Tailscale documentation - subnet routers and site-to-site connectivity (tailscale.com/kb)
- Wi-Fi Alliance - WPA3 security overview (wi-fi.org/discover-wi-fi/security)
