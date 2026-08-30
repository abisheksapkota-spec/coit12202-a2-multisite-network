# Rubric checklist — path to 45/45

Check items off as you go. Commit this file to the repo and update it honestly — it's also evidence of planning for the Professionalism criterion.

**Reading the annotations:** boxes only get checked when the *whole team* has met a criterion — most of Section 1–4's items genuinely require all three sites, so they stay unchecked even where one site is done. Where one site has real progress, that's noted inline instead of checking the box early.

## Gates — must clear all three, independent of technical quality

- [x] Shared GitHub repo exists and is what's submitted (not a Word doc / loose ZIP)
- [ ] Commit history shows real incremental work — each member committing their own work over time, not one bulk commit near the deadline
- [ ] Demonstration video submitted, 8–12 min, all members speak and show their face at least once

## 1. Enterprise authentication & federation — /6

- [ ] Tailscale federation live between all three sites (screenshot/log of all three nodes in the tailnet)
- [ ] Each site has a working local KDC (own realm, e.g. `SITEA.LOCAL`) — *Site A: done and proven. B/C: not started.*
- [ ] Cross-realm trust configured both directions for every site pair
- [ ] **Actual cross-site login demonstrated** — `klist` showing the ticket chain through the other site's `krbtgt`, not just local auth per site
- [ ] Config (KDC, principals, keytabs) committed to repo — *Site A: done.*

## 2. Firewalls & zones — /6

- [ ] OPNsense at each site with WAN/LAN/DMZ/MGMT — *Site A: done. Site B: diagram only, not built. Site C: not started.*
- [ ] Rules per zone, same approach/philosophy at all three sites — *Site A: not yet — OPT1/OPT2 currently proven only with the firewall temporarily disabled, not with real saved rules. This needs fixing even for Site A alone.*
- [ ] Screenshots of rules per interface, per site — *blocked on the above.*
- [ ] Live log evidence showing blocked traffic enforced as intended
- [ ] Config committed to repo — *Site A: done.*

## 3. Site-to-site VPN — /6

- [ ] Tunnel(s) up between all site pairs (IPsec or WireGuard) — *Week 7, not started.*
- [ ] Before capture: same cross-site traffic, unprotected, payload readable
- [ ] After capture: same traffic, tunnelled, opaque (ESP / WireGuard)
- [ ] Each capture states: where captured, filter used, what it proves
- [ ] Both .pcap/.pcapng files committed to repo

## 4. Intrusion detection & consistent policy — /6

- [ ] Suricata running at each site — *Week 8, not started.*
- [ ] Same detection policy/ruleset across all three sites
- [ ] Alert(s) demonstrated firing (log or screenshot)
- [ ] Placement of Suricata at each site reasoned in the report, not just stated
- [ ] Config committed to repo

## 5. Security-justification report — /6

- [x] Written as Markdown in the repo — *format done (`report/report.md`); content only complete for Site A's sections so far.*
- [ ] Architecture section — not just described, *justified* — *drafted at the whole-network level; needs Sites B/C folded in once built.*
- [ ] Trust boundaries between sites explained — *not started (Section 7 still scaffold).*
- [ ] Defence-in-depth argument across the whole topology — *not started.*
- [ ] Every major claim cites specific capture evidence, correctly analysed — *true for Site A's drafted sections.*
- [ ] Links to diagrams, screenshots, and files in the repo rather than re-pasting them — *true for Site A's drafted sections.*

## 6. Wireless extension — /3

- [ ] Threats explained (evil twin, deauth, KRACK/handshake capture, rogue AP)
- [ ] Defences justified (WPA3-SAE/Enterprise, 802.1X + EAP-TLS/RADIUS, management-frame protection, SSID/VLAN segmentation, wireless IDS)
- [ ] Explicitly tied to *this* architecture and trust boundaries, not generic

## 7. Network diagram — /3

- [x] `.drawio` file in the repo — *file exists, but is still the original 3-box starter template.*
- [ ] Matches the actual build (not the original plan) — recheck after any late changes — *not yet — needs updating with Site A's real zone/host detail once B/C exist too.*
- [ ] All three sites, all zones, VPN mesh, clearly labelled

## 8. Group demonstration video — /3

- [ ] Real running systems shown, not slides/screenshots
- [ ] Every member speaks and identifies themself (name + student ID)
- [ ] Roughly equal speaking time
- [ ] 8–12 minutes
- [ ] Submitted via Echo360

## 9. Individual contribution — /3

- [ ] Own GNS3 project export included — **⚠ gap: Site A's `.gns3project` file is not yet in the repo — only screenshots/configs are. This is a concrete, easy fix worth doing before anything else on this list.**
- [ ] Regular own commits across the project timeline, not clustered at the end — *can't be assessed from a file snapshot — check your actual commit history/dates.*
- [ ] Clear, identifiable role in the build — *Site A: yes, documented throughout.*

## 10. Professionalism — /3

- [ ] GitHub Project (Kanban) kept current throughout, not backfilled
- [ ] Teams channel shows active group communication
- [ ] Repo structure and file naming follow the spec's required formats
- [ ] Submission requirements followed exactly (repo link, ZIP, video)
