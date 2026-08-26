# Rubric checklist — path to 45/45

Check items off as you go. Commit this file to the repo and update it honestly — it's also evidence of planning for the Professionalism criterion.

## Gates — must clear all three, independent of technical quality

- [ ] Shared GitHub repo exists and is what's submitted (not a Word doc / loose ZIP)
- [ ] Commit history shows real incremental work — each member committing their own work over time, not one bulk commit near the deadline
- [ ] Demonstration video submitted, 8–12 min, all members speak and show their face at least once

## 1. Enterprise authentication & federation — /6

- [ ] Tailscale federation live between all three sites (screenshot/log of all three nodes in the tailnet)
- [ ] Each site has a working local KDC (own realm, e.g. `SITEA.LOCAL`)
- [ ] Cross-realm trust configured both directions for every site pair
- [ ] **Actual cross-site login demonstrated** — `klist` showing the ticket chain through the other site's `krbtgt`, not just local auth per site
- [ ] Config (KDC, principals, keytabs) committed to repo

## 2. Firewalls & zones — /6

- [ ] OPNsense at each site with WAN/LAN/DMZ/MGMT
- [ ] Rules per zone, same approach/philosophy at all three sites
- [ ] Screenshots of rules per interface, per site
- [ ] Live log evidence showing blocked traffic enforced as intended
- [ ] Config committed to repo

## 3. Site-to-site VPN — /6

- [ ] Tunnel(s) up between all site pairs (IPsec or WireGuard)
- [ ] Before capture: same cross-site traffic, unprotected, payload readable
- [ ] After capture: same traffic, tunnelled, opaque (ESP / WireGuard)
- [ ] Each capture states: where captured, filter used, what it proves
- [ ] Both .pcap/.pcapng files committed to repo

## 4. Intrusion detection & consistent policy — /6

- [ ] Suricata running at each site
- [ ] Same detection policy/ruleset across all three sites
- [ ] Alert(s) demonstrated firing (log or screenshot)
- [ ] Placement of Suricata at each site reasoned in the report, not just stated
- [ ] Config committed to repo

## 5. Security-justification report — /6

- [ ] Written as Markdown in the repo
- [ ] Architecture section — not just described, *justified*
- [ ] Trust boundaries between sites explained
- [ ] Defence-in-depth argument across the whole topology
- [ ] Every major claim cites specific capture evidence, correctly analysed
- [ ] Links to diagrams, screenshots, and files in the repo rather than re-pasting them

## 6. Wireless extension — /3

- [ ] Threats explained (evil twin, deauth, KRACK/handshake capture, rogue AP)
- [ ] Defences justified (WPA3-SAE/Enterprise, 802.1X + EAP-TLS/RADIUS, management-frame protection, SSID/VLAN segmentation, wireless IDS)
- [ ] Explicitly tied to *this* architecture and trust boundaries, not generic

## 7. Network diagram — /3

- [ ] `.drawio` file in the repo
- [ ] Matches the actual build (not the original plan) — recheck after any late changes
- [ ] All three sites, all zones, VPN mesh, clearly labelled

## 8. Group demonstration video — /3

- [ ] Real running systems shown, not slides/screenshots
- [ ] Every member speaks and identifies themself (name + student ID)
- [ ] Roughly equal speaking time
- [ ] 8–12 minutes
- [ ] Submitted via Echo360

## 9. Individual contribution — /3

- [ ] Own GNS3 project export included
- [ ] Regular own commits across the project timeline, not clustered at the end
- [ ] Clear, identifiable role in the build

## 10. Professionalism — /3

- [ ] GitHub Project (Kanban) kept current throughout, not backfilled
- [ ] Teams channel shows active group communication
- [ ] Repo structure and file naming follow the spec's required formats
- [ ] Submission requirements followed exactly (repo link, ZIP, video)
