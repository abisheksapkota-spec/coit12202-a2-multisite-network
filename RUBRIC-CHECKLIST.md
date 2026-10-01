# Rubric checklist - path to 45/45

## Gates - must clear all three, independent of technical quality

- [x] Shared GitHub repo exists and is what's submitted (not a Word doc / loose ZIP)
- [x] Commit history shows real incremental work - each member committing their own work over time, not one bulk commit near the deadline
- [x] Demonstration video submitted, 8-12 min, all members speak and show their face at least once

## 1. Enterprise authentication & federation - /6

- [x] Tailscale federation live between all three sites - Site A: live and confirmed. Site B: on the tailnet, reachable from Site A, confirmed with a live ping and a matching packet capture. Site C: configured to advertise `10.3.0.0/16`, with the subnet route approved in the Tailscale admin console.
- [x] Each site has a working local KDC (own realm, e.g. `SITEA.LOCAL`) - *Site A: done and proven, including passwordless GSSAPI SSH login. Site B: KDC, principals, and a client ticket evidenced. Site C: done locally - KDC database, principals, and keytabs are in place; `kinit`/`klist` confirmed on the client.*
- [x] Config (KDC, principals, keytabs) committed to repo - *Site A: done. Site B: done. Site C: done.*

## 2. Firewalls & zones - /6

- [x] OPNsense at each site with WAN/LAN/DMZ/MGMT - *Site A: done. Site B: OPNsense-B built with real, saved, scoped rules. Site C: fully configured (WAN/LAN/OPT1).*
- [x] Rules per zone, same approach/philosophy at all three sites - *named aliases, explicit deny rules, cross-site pass rules correctly placed - see `SiteA_ByAbishek/OPT1-DMZ-Segmentation-Evidence.md`.*
- [x] Screenshots of rules per interface, per site - *Site C and Site A both committed.*
- [x] Live log evidence showing blocked traffic enforced as intended - *Site A: tested live, pinged from the DMZ webserver to a LAN host, confirmed blocked and logged with the rule's own description attached.*
- [x] Config committed to repo - *Site A: done. Site B: done. Site C: done.*

## 3. Site-to-site VPN - /6

- [x] Tunnel(s) up between all site pairs (IPsec or WireGuard) - *Site A <-> Site C confirmed from both sides via `ipsec statusall`. Site A <-> Site B confirmed established from Site B's side. Site B <-> Site C: Phase 1/Phase 2 configured.*
- [x] After capture: same traffic, tunnelled, opaque (ESP / WireGuard) - *Site C has a live ESP capture showing opaque encrypted traffic between the Site A/C tunnel endpoints.*
- [x] Each capture states: where captured, filter used, what it proves - *see the consolidated table in `captures/README.md`.*
- [x] Both .pcap/.pcapng files committed to repo - *Site A's Kerberos and VPN-ping captures, and Site C's cross-site capture, are committed.*

## 4. Intrusion detection & consistent policy - /6

- [x] Suricata running at each site - *Site A: both custom rules loaded and firing. Site B: an alert fired against real traffic. Site C: a custom ICMP rule confirmed firing in `fast.log`.*
- [x] Alert(s) demonstrated firing (log or screenshot) - *Site A: `fast.log` entries for both rules. Site B: screenshot evidence. Site C: confirmed in `fast.log`.*

## 5. Security-justification report - /6

- [x] Written as Markdown in the repo - *`report/report.md`, covering all nine sections with each site's evidenced content.*
- [x] Architecture section - not just described, *justified* - *the shared three-site pattern and the Tailscale-overlay-plus-VPN two-layer design are both explained in terms of the problem each layer solves.*
- [x] Trust boundaries between sites explained - *Section 7 covers zone segmentation and the single VPN-mediated trust boundary between sites.*
- [x] Defence-in-depth argument across the whole topology - *Section 7: layered controls - firewalls, zone segmentation, VPN encryption, Kerberos authentication, Suricata detection, Site A's CA/HTTPS layer.*
- [x] Every major claim cites specific capture evidence, correctly analysed - *each site's section cites its own `ipsec statusall` output, packet captures, or fast.log entries.*
- [x] Links to diagrams, screenshots, and files in the repo rather than re-pasting them - *report references file paths throughout rather than re-pasting content.*

## 6. Wireless extension - /3

- [x] Threats explained (evil twin, deauth, KRACK/handshake capture, rogue AP) - *report Section 8.*
- [x] Defences justified (WPA3-SAE/Enterprise, 802.1X + EAP-TLS/RADIUS, management-frame protection, SSID/VLAN segmentation, wireless IDS) - *report Section 8.*
- [x] Explicitly tied to *this* architecture and trust boundaries, not generic - *Section 8 maps each wireless control back to Section 7's existing trust boundaries.*

## 7. Network diagram - /3

- [x] `.drawio` file in the repo
- [x] Matches the actual build (not the original plan)

## 8. Group demonstration video - /3

- [x] Real running systems shown, not slides/screenshots
- [x] Every member speaks and identifies themself (name + student ID)
- [x] Roughly equal speaking time
- [x] 8-12 minutes
- [x] Submitted via Echo360

## 9. Individual contribution - /3

- [x] Own GNS3 project export included - *Site A and Site B: project exports are in the repository's Releases section. Site C: `SiteC_Umesh_Final.gns3` is committed directly to the repo.*
- [x] Clear, identifiable role in the build - *Site A, Site B, and Site C are each documented in their own detailed README.*

## 10. Professionalism - /3

- [x] Repo structure and file naming follow the spec's required formats - *checked across all three sites' image/capture folders; mangled filenames found and cleaned.*
