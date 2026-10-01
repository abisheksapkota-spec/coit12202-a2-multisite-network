# Rubric checklist - path to 45/45

Check items off as you go. Commit this file to the repo and update it honestly - it's also evidence of planning for the Professionalism criterion.

**Reading the annotations:** boxes only get checked when the *whole team* has met a criterion - most of Section 1-4's items genuinely require all three sites, so they stay unchecked even where one site is done. Where one site has real progress, that's noted inline instead of checking the box early.

## Gates - must clear all three, independent of technical quality

- [x] Shared GitHub repo exists and is what's submitted (not a Word doc / loose ZIP)
- [ ] Commit history shows real incremental work - each member committing their own work over time, not one bulk commit near the deadline
- [ ] Demonstration video submitted, 8-12 min, all members speak and show their face at least once

## 1. Enterprise authentication & federation - /6

- [ ] Tailscale federation live between all three sites (screenshot/log of all three nodes in the tailnet) - Site A: live and confirmed. Site B: on the tailnet, reachable from Site A - re-confirmed this morning with a live ping from a real Site A LAN host (`CLIENT`, `10.1.10.30`) to Site B's VPN router (`10.2.9.2`), 5/5 packets, 0% loss, terminal screenshot plus a matching packet capture (see `SiteA_ByAbishek/captures/README.md`). Site C: configured to advertise `10.3.0.0/16`, and the subnet route is confirmed **approved** in the Tailscale admin console (screenshot verified) - correcting the earlier "not yet joined" status. Still need a screenshot/log showing all three nodes together in the tailnet.
- [ ] Each site has a working local KDC (own realm, e.g. `SITEA.LOCAL`) - *Site A: done and proven. Site B: KDC, principals, and a client ticket already evidenced - GSSAPI login not yet confirmed. Site C: now done locally - KDC database, principals, and keytabs are in place; `kinit`/`klist` confirmed on the client. Note: the image filed as `09_SiteC_Kerberos_ServerC_Keytab_Authentication_Success.png` does not actually show keytab authentication (it shows the IDS-C console/Suricata build info) - the server-side keytab-auth claim needs a correct screenshot before it can be counted as evidenced.*
- [ ] Cross-realm trust configured both directions for every site pair
- [ ] **Actual cross-site login demonstrated** - `klist` showing the ticket chain through the other site's `krbtgt`, not just local auth per site
- [ ] Config (KDC, principals, keytabs) committed to repo - *Site A: done. Site C: done.*

## 2. Firewalls & zones - /6

- [ ] OPNsense at each site with WAN/LAN/DMZ/MGMT - *Site A: done. Site B: OPNsense-B now built with real, saved, scoped rules (WAN rule applied, confirmed via screenshot) - ahead of the "diagram only" status previously recorded here; her README/RUBRIC status still needs updating to match. Site C: fully configured (WAN/LAN/OPT1), including the WAN rule fix so the source is correctly scoped to Site A's and Site B's transit ranges rather than Site C's own.*
- [x] Rules per zone, same approach/philosophy at all three sites - *Site A: done - OPNsense2 has named aliases (`SITE_A_LAN` 10.1.10.0/24, `SITE_A_DMZ` 10.1.20.0/24), a real saved OPT1 rule (explicit deny `SITE_A_DMZ` -> `SITE_A_LAN`), and the cross-site pass rules (10.2.0.0/16, 10.3.0.0/16 -> `SITE_A_LAN`) correctly sit on the WAN tab, confirmed via screenshot - the earlier LAN-tab placement mistake was caught and fixed. Minor cleanup: leftover inactive copies of those two rules still sit on the LAN tab too and should be deleted (harmless, just untidy). See `SiteA_ByAbishek/OPT1-DMZ-Segmentation-Evidence.md`. Site C: rules are saved and scoped correctly (aliases `SITE_C_LAN`/`SITE_C_DMZ`).*
- [x] Screenshots of rules per interface, per site - *Site C has these committed. Site A: rule-list, alias, and live-block-log screenshots all committed - see `SiteA_ByAbishek/OPT1-DMZ-Segmentation-Evidence.md`.*
- [x] Live log evidence showing blocked traffic enforced as intended - *Site A: tested live - pinged from the DMZ webserver (10.1.20.20) to a LAN host (10.1.10.20), confirmed blocked, and captured the block firing in Firewall > Log Files > Live View with the rule's own description attached to each log line.*
- [ ] Config committed to repo - *Site A: done. Site C: done.*

## 3. Site-to-site VPN - /6

- [ ] Tunnel(s) up between all site pairs (IPsec or WireGuard) - *Site A <-> Site C: now confirmed from BOTH sides - Site C's tcpdump/ESP capture, and Site A's own `ipsec statusall` on OPNsense1 showing `con2` ESTABLISHED, CHILD_SA INSTALLED, `10.1.0.0/16 === 10.3.0.0/16`. Bidirectional LAN-to-LAN traffic (not just the tunnel) still needs proving. Site A <-> Site B: now confirmed established from Site B's side too - OPNsense-B's own `ipsec statusall` shows `con1` ESTABLISHED, INSTALLED, TUNNEL `10.2.0.0/16 === 10.1.0.0/16`, with real ESP traffic counted (691 packets). Still need Site A's own console to confirm the same tunnel from its end. Separately, the Tailscale underlay that this tunnel rides on was re-confirmed this morning with a live ping from a real Site A LAN host (`CLIENT`, `10.1.10.30`) through `OPNsense1` to Site B's VPN router (`10.2.9.2`), 5/5, 0% loss, with a matching packet capture - this is underlay reachability, not a new IPsec-tunnel-state check, but it is further confirmation the path the tunnel needs is actually there. Site B <-> Site C: Phase 1/Phase 2 configuration status only, not yet confirmed established.*
- [ ] Before capture: same cross-site traffic, unprotected, payload readable
- [ ] After capture: same traffic, tunnelled, opaque (ESP / WireGuard) - *Site C has a live ESP capture (screenshot verified) showing opaque encrypted traffic between the Site A/C tunnel endpoints; still need the matching "before" (unprotected) capture and the two to be paired with a written explanation per the rubric's own requirement below.*
- [ ] Each capture states: where captured, filter used, what it proves
- [ ] Both .pcap/.pcapng files committed to repo - *screenshots exist for Site C's ESP traffic; the actual .pcap/.pcapng file itself still needs to be committed, not just the screenshot.*

## 4. Intrusion detection & consistent policy - /6

- [ ] Suricata running at each site - *Site A: done, both rules loaded and firing (evidenced). Site B: configured, with an alert fired against real traffic - evidenced in screenshots (`15-Suricata-B-Configuration`, `16-Suricata-B-Alert-Detection`, `Suricata-B-Real-Traffic-Detection` in `SiteB_ByAnima/Images/README.md`), correcting the earlier "not yet confirmed" status - the exact ruleset/rule IDs and the same independent cross-check pattern Site A and Site C used still need writing up directly from Anima. Site C: running, with a custom `SITE-C ICMP Ping Detected` rule confirmed firing in `fast.log`.*
- [ ] Same detection policy/ruleset across all three sites - *worth checking all three rule sets side by side, since they were built independently and the exact rule IDs for Site B aren't written up yet.*
- [ ] Alert(s) demonstrated firing (log or screenshot) - *Site A and Site C both have this confirmed in writing; Site B has screenshot evidence of an alert against real traffic but it isn't written up with the same detail yet.*
- [ ] Placement of Suricata at each site reasoned in the report, not just stated
- [ ] Config committed to repo

## 5. Security-justification report - /6

- [x] Written as Markdown in the repo - *format done (`report/report.md`); content only complete for Site A's sections so far.*
- [ ] Architecture section - not just described, *justified* - *drafted at the whole-network level; needs Sites B/C folded in once built.*
- [ ] Trust boundaries between sites explained - *not started (Section 7 still scaffold).*
- [ ] Defence-in-depth argument across the whole topology - *not started. Site A's new CA/HTTPS work (private root+intermediate CA issuing a cert for `www.sitea.local`, verified end-to-end from a client) is a good candidate for this section, since it adds an application-layer trust mechanism on top of the network-layer controls - it doesn't map to its own rubric line, so it should be folded in here rather than left standalone.*
- [ ] Every major claim cites specific capture evidence, correctly analysed - *true for Site A's drafted sections.*
- [ ] Links to diagrams, screenshots, and files in the repo rather than re-pasting them - *true for Site A's drafted sections.*

## 6. Wireless extension - /3

- [ ] Threats explained (evil twin, deauth, KRACK/handshake capture, rogue AP)
- [ ] Defences justified (WPA3-SAE/Enterprise, 802.1X + EAP-TLS/RADIUS, management-frame protection, SSID/VLAN segmentation, wireless IDS)
- [ ] Explicitly tied to *this* architecture and trust boundaries, not generic

## 7. Network diagram - /3

- [x] `.drawio` file in the repo - *file exists, but is still the original 3-box starter template.*
- [ ] Matches the actual build (not the original plan) - recheck after any late changes - *not yet - needs updating with real zone/host detail for all three sites now that Site C's build is substantially complete too.*
- [ ] All three sites, all zones, VPN mesh, clearly labelled

## 8. Group demonstration video - /3

- [ ] Real running systems shown, not slides/screenshots
- [ ] Every member speaks and identifies themself (name + student ID)
- [ ] Roughly equal speaking time
- [ ] 8–12 minutes
- [ ] Submitted via Echo360

## 9. Individual contribution - /3

- [ ] Own GNS3 project export included - **Site A: still missing - `.gns3project` file is not yet in the repo, only screenshots/configs are. Site C: done - `SiteC_Umesh_Final.gns3` is now committed. Site B: not yet.** So this is no longer "no site has one" - it's now a Site A/B-specific gap, and worth fixing for Site A first since it's the easy, no-dependency fix it always was.
- [ ] Regular own commits across the project timeline, not clustered at the end - *can't be assessed from a file snapshot - check your actual commit history/dates.*
- [ ] Clear, identifiable role in the build - *Site A: yes, documented throughout. Site C: yes, documented in Umesh's own README.*

## 10. Professionalism - /3

- [ ] GitHub Project (Kanban) kept current throughout, not backfilled
- [ ] Teams channel shows active group communication
- [ ] Repo structure and file naming follow the spec's required formats - *`SiteC_ByUmesh/SITE C IMAGES/` checked: several files do share duplicate numeric prefixes (two different images both named `02_...`, `03_...`, etc., up to `13_`), but their full filenames differ after the shared number (e.g. `02_SiteC_IPsec_Tunnel_Established.png` vs `02_SiteC_OPNsense_Interface_Assignment.png`) - on inspection this is cosmetic numbering overlap only, not a real ambiguity, so left as-is. `SiteB_ByAnima/Images/` had ~19 genuinely mangled local filenames (double extensions, trailing periods/spaces, one emoji-code fragment, one "- Copy") - confirmed unreferenced (Anima's own `Images/README.md` embeds all her screenshots via GitHub CDN links, not these local files) and renamed to clean names for a tidy file listing.*
- [ ] Submission requirements followed exactly (repo link, ZIP, video)
