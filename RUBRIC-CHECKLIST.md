# Rubric checklist - path to 45/45

Check items off as you go. Commit this file to the repo and update it honestly - it's also evidence of planning for the Professionalism criterion.

**Reading the annotations:** boxes only get checked when the *whole team* has met a criterion - most of Section 1-4's items genuinely require all three sites, so they stay unchecked even where one site is done. Where one site has real progress, that's noted inline instead of checking the box early.

## Gates - must clear all three, independent of technical quality

- [x] Shared GitHub repo exists and is what's submitted (not a Word doc / loose ZIP)
- [ ] Commit history shows real incremental work - each member committing their own work over time, not one bulk commit near the deadline
- [ ] Demonstration video submitted, 8-12 min, all members speak and show their face at least once

## 1. Enterprise authentication & federation - /6

- [ ] Tailscale federation live between all three sites (screenshot/log of all three nodes in the tailnet) - Site A: live and confirmed. Site B: connected to the tailnet, route advertised. Site C: not yet joined - final route approval/verification still pending per Umesh's own notes.
- [ ] Each site has a working local KDC (own realm, e.g. `SITEA.LOCAL`) - *Site A: done and proven. Site B: KDC, principals, keytab, a client ticket, a service ticket, and GSSAPI SSH shown enabled - a full positive/negative login proof (like Site A's) not yet confirmed, and not yet written up in her own README. Site C: not started - listed as still to-do in Umesh's own README.*
- [ ] Cross-realm trust configured both directions for every site pair
- [ ] **Actual cross-site login demonstrated** - `klist` showing the ticket chain through the other site's `krbtgt`, not just local auth per site
- [ ] Config (KDC, principals, keytabs) committed to repo - *Site A: done. Site B: evidenced in screenshots, not yet committed as write-up/config files.*

## 2. Firewalls & zones - /6

- [ ] OPNsense at each site with WAN/LAN/DMZ/MGMT - *Site A: done. Site B: built and addressed (screenshots show interface addressing, WAN rules, ping tests both ways) - not yet written up in her own README. Site C: done (WAN/LAN/DMZ addressed, WAN rule scoping corrected).*
- [ ] Rules per zone, same approach/philosophy at all three sites - *Site A: not yet - OPT1/OPT2 currently proven only with the firewall temporarily disabled, not with real saved rules. This needs fixing even for Site A alone.*
- [ ] Screenshots of rules per interface, per site - *Site A and C: available. Site B: available as loose screenshots, needs folding into her README.*
- [ ] Live log evidence showing blocked traffic enforced as intended
- [ ] Config committed to repo - *Site A: done. Site C: done.*

## 3. Site-to-site VPN - /6

- [ ] Tunnel(s) up between all site pairs (IPsec or WireGuard) - *Site A <-> Site C: Phase 1 + Phase 2 now configured symmetrically on both ends, and the WAN-upstream-gateway bug that blocked traffic has been fixed on both sides - re-test for actual connectivity is the immediate next step. Site A <-> Site B: Phase 1 + Phase 2 configured on Site A's side, Site B's status not yet confirmed. Site B <-> Site C: not yet started.*
- [ ] Before capture: same cross-site traffic, unprotected, payload readable
- [ ] After capture: same traffic, tunnelled, opaque (ESP / WireGuard)
- [ ] Each capture states: where captured, filter used, what it proves
- [ ] Both .pcap/.pcapng files committed to repo

## 4. Intrusion detection & consistent policy - /6

- [ ] Suricata running at each site - *Site A: done, both rules loaded and firing (evidenced). Site B: rules configured and an alert fired against real traffic (evidenced in screenshots, not yet written up). Site C: a custom rule loaded and an ICMP alert fired (evidenced in his own README).*
- [ ] Same detection policy/ruleset across all three sites - *not yet confirmed - each site's exact ruleset needs comparing once all three are documented.*
- [ ] Alert(s) demonstrated firing (log or screenshot) - *Site A, B, and C all have at least one alert evidenced.*
- [ ] Placement of Suricata at each site reasoned in the report, not just stated
- [ ] Config committed to repo - *Site A: done. Site C: done. Site B: pending her write-up.*

## 5. Security-justification report - /6

- [x] Written as Markdown in the repo - *format done (`report/report.md`); Sections 3, 4, 5, 6 have real content for Site A, with Sections 3/4/6 also reflecting Site B and Site C's current progress where evidenced.*
- [ ] Architecture section - not just described, *justified* - *drafted at the whole-network level; needs Sites B/C folded in fully once their own write-ups exist.*
- [ ] Trust boundaries between sites explained - *not started (Section 7 still scaffold).*
- [ ] Defence-in-depth argument across the whole topology - *not started.*
- [ ] Every major claim cites specific capture evidence, correctly analysed - *true for Site A's drafted sections; Site B/C sections currently cite screenshot evidence where it exists, pending their own repo write-ups for full linkage.*
- [ ] Links to diagrams, screenshots, and files in the repo rather than re-pasting them - *true for Site A's drafted sections.*

## 6. Wireless extension - /3

- [ ] Threats explained (evil twin, deauth, KRACK/handshake capture, rogue AP)
- [ ] Defences justified (WPA3-SAE/Enterprise, 802.1X + EAP-TLS/RADIUS, management-frame protection, SSID/VLAN segmentation, wireless IDS)
- [ ] Explicitly tied to *this* architecture and trust boundaries, not generic

## 7. Network diagram - /3

- [x] `.drawio` file in the repo - *file exists, but is still the original 3-box starter template.*
- [ ] Matches the actual build (not the original plan) - recheck after any late changes - *not yet - needs updating with all three sites' real zone/host detail.*
- [ ] All three sites, all zones, VPN mesh, clearly labelled

## 8. Group demonstration video - /3

- [ ] Real running systems shown, not slides/screenshots
- [ ] Every member speaks and identifies themself (name + student ID)
- [ ] Roughly equal speaking time
- [ ] 8-12 minutes
- [ ] Submitted via Echo360

## 9. Individual contribution - /3

- [ ] Own GNS3 project export included - **⚠ gap: no site has a `.gns3project` file in the repo yet - only screenshots/configs are there for any of the three. This is a concrete, easy fix worth doing before anything else on this list, for all three of us.**
- [ ] Regular own commits across the project timeline, not clustered at the end - *can't be assessed from a file snapshot - check your actual commit history/dates.*
- [ ] Clear, identifiable role in the build - *Site A: yes, documented throughout. Site C: yes, documented throughout. Site B: real work exists, but isn't yet identifiable in the repo until her README/ADDRESSING write-up lands.*

## 10. Professionalism - /3

- [ ] GitHub Project (Kanban) kept current throughout, not backfilled
- [ ] Teams channel shows active group communication
- [ ] Repo structure and file naming follow the spec's required formats
- [ ] Submission requirements followed exactly (repo link, ZIP, video)
