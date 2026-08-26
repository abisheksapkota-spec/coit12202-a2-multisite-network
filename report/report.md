# Secured Multi-Site Network — Security Justification Report

**Team:** Abishek (Site A, team lead) · Anima (Site B) · Umesh (Site C)
**Unit:** COIT12202 — Assessment 2

> Delete this line and every instruction line below as you write. Emphasise *justification* — why the design is secure — not just description. Cite specific evidence (captures, screenshots, config excerpts) for every claim.

## 1. Scenario and assumptions

*Restate the scenario briefly. List only the assumptions your work actually depends on — see the Scenario section of the spec.*

## 2. Architecture overview

*Reference `network-diagram.drawio` and the addressing scheme in `ADDRESSING.md`. Explain the Tailscale-overlay vs VPN-tunnel distinction — why both layers exist.*

## 3. Firewalls and zones

*Per site: zone design, key rules, and why each rule exists. Link to screenshots in each `SiteX_By.../Images/` folder.*

## 4. Enterprise authentication (Kerberos)

*Per-site KDC setup, then cross-realm trust between all three. Include the `klist` ticket-chain evidence proving cross-site auth actually works, not just local login per site.*

## 5. Site-to-site VPN

*Tunnel type and topology (mesh vs hub-spoke), why chosen. **Required:** reference the before/after pcap pair in `captures/` — state where captured, filter used, and what it proves.*

## 6. Intrusion detection (Suricata)

*Ruleset used, why placed where it is at each site, evidence of an alert firing.*

## 7. Defence-in-depth and trust boundaries

*Tie the previous sections together — where do overlapping controls exist? Where is trust granted between sites, and why is that boundary justified rather than just convenient?*

## 8. Wireless extension (required — design only)

*Wireless-specific threats (e.g. evil twin, deauthentication, KRACK/4-way-handshake capture, rogue access points) and the defences you'd deploy to extend this architecture to wireless (e.g. WPA3-SAE/Enterprise, 802.1X with EAP-TLS and RADIUS, management-frame protection, SSID-to-VLAN segmentation, wireless IDS). Tie every choice back to your existing trust boundaries — nothing wireless is built.*

## 9. Conclusion

*Brief summary of what was achieved and any known limitations.*

## References

*List sources cited above.*
