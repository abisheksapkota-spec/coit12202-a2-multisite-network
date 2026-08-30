# Secured Multi-Site Network — Security Justification Report

**Team:** Abishek (Site A, team lead) · Anima (Site B) · Umesh (Site C)
**Unit:** COIT12202 — Assessment 2

> **Draft status:** Sections 1–4 are drafted from Site A's completed build, with real evidence cited throughout. Sections 5–9 remain scaffolded (in *italics*) until VPN, Suricata, and Sites B/C exist — they need real content before submission, not filling in early with guesses. Anima and Umesh: add your sites' material into sections 3 and 4 alongside Site A's rather than replacing it, since the report needs to justify all three, not just one.

## 1. Scenario and assumptions

This build implements the assessment's scenario directly: three branch sites, one owned end-to-end by each group member, federated into a single security-consistent network. The assumptions below are ones the build actually depends on, not a general restatement of the spec.

- **WAN represents each site's path to the internet** via the host machine's own network adapter/NAT, not a real ISP circuit — reflected in how each site's Cloud/NAT node is wired directly to OPNsense's WAN interface.
- **Tailscale (or an equivalent bridged reachability layer)** solves a specific problem: three independently-hosted GNS3 topologies, each on a different physical machine, have no shared network to plug into. It is the *underlay* that makes cross-site reachability possible at all — it is not the encrypted link itself.
- **The site-to-site VPN tunnel (IPsec/WireGuard, Week 7)** is the actual encrypted, audited connection carrying real inter-site traffic, built on top of that underlay. This two-layer design is deliberate: the assignment's "federate the sites" and "site-to-site VPN" requirements are two separate layers solving two separate problems, not duplicate work.
- **Addressing is agreed centrally** (`ADDRESSING.md`) before individual builds, with each site owning a distinct `/16` (Site A `10.1.0.0/16`, Site B `10.2.0.0/16`, Site C `10.3.0.0/16`) so that once tunnels exist, routing between sites is unambiguous.
- **MGMT hosts administrative traffic only** — nothing in MGMT is reachable from LAN or DMZ by default, and inter-zone access requires an explicit rule, not an implicit trust.

## 2. Architecture overview

Each site runs the same pattern: an OPNsense firewall with four zones (WAN, LAN, DMZ, MGMT), a local Kerberos KDC serving that site's realm, and — once Week 8 material is confirmed — a Suricata sensor watching the LAN/WAN boundary. The three sites are identical in *structure* and differ only in addressing and realm name, which is what makes the eventual cross-realm trust and VPN mesh tractable: every site expects the same shape from its peers.

The Tailscale-overlay-plus-VPN-tunnel distinction (Section 1) is the central architectural decision of this build. Without it, there would be no way to get three separately-hosted GNS3 instances talking to each other at all; without the VPN layer on top, whatever traffic did cross between sites would be unencrypted and unaudited. Site A's OPNsense firewall additionally demonstrates why this distinction had to be understood correctly rather than assumed: an initial interface assignment mistake (Section 3) put WAN and LAN on the wrong physical adapters relative to what the console believed, which would have silently broken this exact design if left uncaught.

*(Reference `network-diagram.drawio` for the full multi-site diagram once Sites B and C exist. Currently reflects Site A only plus the planned mesh shape.)*

## 3. Firewalls and zones

### Site A (Abishek) — complete

Site A's OPNsense firewall (`OPNsense1`) implements the four-zone pattern with the following addressing, confirmed working end-to-end:

| Zone | Interface | Subnet | Gateway |
|---|---|---|---|
| WAN | vtnet0 | Tailscale/DHCP-assigned | — |
| LAN | vtnet1 | `10.1.10.0/24` | `10.1.10.1` |
| DMZ (OPT1) | vtnet2 | `10.1.20.0/24` | `10.1.20.1` |
| MGMT (OPT2) | vtnet3 | `10.1.30.0/24` | `10.1.30.1` |

**A genuine mistake worth documenting as justification, not hiding:** the first interface assignment put WAN on `vtnet1` and LAN on `vtnet0` — the reverse of the actual physical wiring — because the console's WAN auto-detect failed and a manual guess was made without cross-checking the GNS3 topology file. This produced a confusing symptom (LAN's gateway address existed, but on the wrong physical link, so nothing on the LAN switch could ever reach it) that took a systematic elimination process to diagnose: testing the switch in isolation with a throwaway VPCS node (ruling the switch itself in or out), then inspecting the exported `.gns3project` topology JSON directly to compare the *declared* adapter-to-device wiring against the *console's* interface assignment. The mismatch was exact and immediately explained every symptom observed. The fix — reassigning WAN↔LAN to match the real wiring rather than the initial guess — resolved connectivity immediately. This is included here because it demonstrates the zone design being *understood*, not just clicked into place: the fix required knowing what each zone's traffic path was supposed to look like in order to recognise it was wrong.

**Firewall rules:** LAN carries OPNsense's default allow-outbound rule. DMZ and MGMT (OPT1/OPT2) start with no rules by default — this is the platform's correct least-privilege behaviour, not a bug — so explicit Pass rules were required before `web-a` and `admin-a` could reach anything, including their own gateway. *(Rules currently only proven with the firewall temporarily disabled for testing — see Section 7 for why leaving it there is not acceptable, and what the real fix requires.)*

**Evidence:** `SiteA_ByAbishek/Images/network-diagram.png`, `opnsense-interfaces-final.png`, `ping-kdc.png`, `ping-server.png`, `ping-client.png`; full config summary in `SiteA_ByAbishek/configs/site-a-network-config.md`.

### Site B (Anima) — not yet started

*Diagram drafted (edge router + OPNsense + three zones + a bastion host in DMZ, a genuine defence-in-depth addition worth expanding on here once built). Addressing and rules not yet confirmed against `ADDRESSING.md`.*

### Site C (Umesh) — not yet started

*Not yet shared with the group.*

## 4. Enterprise authentication (Kerberos)

### Site A (Abishek) — realm `SITEA.LOCAL`, complete and proven

Site A runs its own Kerberos realm across three hosts on the LAN zone: `kdc` (`10.1.10.10`), `server` (`10.1.10.20`), and `client` (`10.1.10.30`). The realm was renamed from the individual Week 5 lab's `EXAMPLE.COM` to `SITEA.LOCAL` throughout `/etc/krb5.conf` and `/etc/hosts` on all three hosts, and re-addressed from the standalone `10.10.1.0/24` lab network into Site A's real LAN, behind OPNsense.

The KDC database was initialised (`kdb5_util create -s`), and three principals created: `student` (a user principal), and `host/server.sitea.local` / `host/client.sitea.local` (service principals). The server's keytab was exported and transferred to `server` over HTTP within the LAN — a deliberately simple transfer method requiring no pre-shared credentials, appropriate given the transfer happens entirely within an already-trusted zone.

**A second genuine bug, also worth including as justification:** once GSSAPI was enabled on `server`'s SSH daemon, authentication failed with `No key table entry found matching host/server@` — notably, with no realm suffix at all. The keytab and `krb5.conf` were both confirmed correct by direct inspection, which narrowed the fault to something outside Kerberos's own configuration. The cause was the Docker container's live kernel hostname (`server`, set by the container runtime at start) not matching the FQDN the keytab was issued under (`server.sitea.local`) — despite `/etc/hostname` *appearing* correct, because editing that file does not itself change the running hostname. Setting the hostname live (`hostname server.sitea.local`) and restarting the SSH daemon resolved it immediately. This distinction — between a config file's stated value and a running process's actual state — is a useful thing to have diagnosed correctly rather than worked around.

**Proof, both directions:** with a valid ticket (`kinit student`), `ssh` to `server` using only `GSSAPIAuthentication`/`PreferredAuthentications=gssapi-with-mic` succeeded with no password prompt. After `kdestroy`, the identical command was refused (`Permission denied`). Running the same test twice with only the ticket's presence changed is the actual evidence that the login mechanism was Kerberos and not something else (e.g. a cached credential or misconfigured fallback).

**Packet capture verified** (`SiteA_ByAbishek/captures/Auth-Kerberos-12312491-client.pcap`, filter `kerberos`): exactly four packets — AS-REQ/AS-REP (from `kinit`) and TGS-REQ/TGS-REP (from the SSH connection's service-ticket request), both exchanges between `client` (`10.1.10.30`) and `kdc` (`10.1.10.10`).

**What's not yet done:** the assignment's actual "cross-site authentication" requirement is cross-*realm* trust between sites, which cannot be built or demonstrated until Sites B and C have their own working KDCs. Site A's realm is built correctly to support that once they exist, but this section currently only demonstrates local (single-site) authentication working end-to-end.

**Evidence:** `SiteA_ByAbishek/Images/kerberos-01` through `-09` (full task sequence), `SiteA_ByAbishek/configs/site-a-kerberos-summary.md` for the complete command-by-command record.

### Site B (Anima) and Site C (Umesh) — not yet built

*No Kerberos hosts exist yet at either site. Once each site has its own KDC (realms `SITEB.LOCAL`, `SITEC.LOCAL`), this section needs each site's own build documented in the same pattern as Site A above, followed by the cross-realm trust configuration and proof that is the actual multi-site deliverable.*

## 5. Site-to-site VPN

*Tunnel type and topology (mesh vs hub-spoke), why chosen. **Required:** reference the before/after pcap pair in `captures/` — state where captured, filter used, and what it proves.*

## 6. Intrusion detection (Suricata)

*Ruleset used, why placed where it is at each site, evidence of an alert firing.*

## 7. Defence-in-depth and trust boundaries

*Tie the previous sections together — where do overlapping controls exist? Where is trust granted between sites, and why is that boundary justified rather than just convenient? (Note for whoever writes this: Site A's current reliance on a temporarily-disabled firewall for DMZ/MGMT testing belongs here as a known gap to close, not as something to omit.)*

## 8. Wireless extension (required — design only)

*Wireless-specific threats (e.g. evil twin, deauthentication, KRACK/4-way-handshake capture, rogue access points) and the defences you'd deploy to extend this architecture to wireless (e.g. WPA3-SAE/Enterprise, 802.1X with EAP-TLS and RADIUS, management-frame protection, SSID-to-VLAN segmentation, wireless IDS). Tie every choice back to your existing trust boundaries — nothing wireless is built.*

## 9. Conclusion

*Brief summary of what was achieved and any known limitations.*

## References

*List sources cited above.*
