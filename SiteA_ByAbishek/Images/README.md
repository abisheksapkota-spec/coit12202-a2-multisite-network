# Images

Screenshots and diagrams for Site A.

![Site A network topology](SiteA_Topology.png)

`SiteA_Topology.png` - the actual GNS3 canvas showing the full built topology (two firewalls, transit segment, LAN, DMZ, VPNRouter1, IDS1/Hub1), with every address labelled.

## What's already here

- Kerberos build evidence (`kerberos-02` through `-04`) - KDC database init, principals/keytab, GSSAPI positive and negative proof
- OPNsense interface banners and the WAN-rule bug fix, before and after (`opnsense1-*`, `opnsense2-*`)
- Tailscale setup and admin console confirmation (`tailscale-01`, `tailscale-02`)
- IPsec Phase 1 to both Site B and Site C (`opnsense1-ipsec-phase1-both-tunnels-enabled.png`)
- IPsec Phase 2 to both peers - Site B (`opnsense1-ipsec-phase2-siteb-configured.png`) and Site C (`opnsense1-ipsec-phase2-sitec-configured.png`), both using full `/16` supernets, Enable IPsec ticked. **Configured, not yet confirmed live** - see "Still to add" below.
- IPsec firewall rules for both peers - WAN auto-generated rules, the IPsec interface Pass rule, and the manual WAN pass rules covering all three sites' transit ranges (`opnsense1-wan-rules-ipsec-siteb-sitec.png`, `opnsense1-ipsec-interface-pass-rule.png`, `opnsense1-wan-rules-manual-pass-all-sites.png`)
- Suricata rules loaded and both alerts fired (`suricata-ids1-*`), plus the traffic that triggered them (`host5-curl-host6-webserver.png`, `host6-access-log-confirms-host5-request.png`)
- DMZ connectivity proof (`topology-dmz-ping-server-to-host6.png`), VNC bridge confirmation (`host1-firefox-vnc-ip-confirm.png`)

## Still to add (Site A only)

- Confirmation the IPsec tunnels are actually connected - `VPN > IPsec > Status Overview` showing Phase 1 green and Phase 2 INSTALLED for both peers, not just the saved config
- VPN before/after packet capture, once the above is confirmed - a capture against an unconnected tunnel would just show plaintext failing for the wrong reason

Naming convention: `<what>-<detail>.png`, e.g. `opnsense1-interfaces.png`, `ping-kdc.png`, `kerberos-03-principals.png`.
