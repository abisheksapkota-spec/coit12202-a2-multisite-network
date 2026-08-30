# Site A — owned by Abishek

- Place the **Site A** GNS3 export (`.gns3project`) directly in this folder.
- `configs/` — network config summary and full Kerberos build record.
- `captures/` — packet capture evidence.
- `Images/` — screenshots: OPNsense zones/interfaces, connectivity, and the full Kerberos build.

**Addressing:** `10.1.0.0/16` (see `ADDRESSING.md` at the repo root) · **Realm:** `SITEA.LOCAL`

## Status

- [x] OPNsense zones (WAN/LAN/DMZ/MGMT) wired and addressed correctly
- [x] LAN, DMZ, MGMT all confirmed pinging their gateway
- [x] Kerberos realm `SITEA.LOCAL` — KDC, principals, keytab, GSSAPI SSH all working and proven (Tasks 1–7 complete)
- [ ] OPT1/OPT2 firewall rules — currently only disabled (`pfctl -d`) for testing; needs real GUI-saved Pass rules before this counts as submitted evidence
- [ ] Site-to-site VPN (Week 7)
- [ ] Suricata IDS (Week 8)
- [ ] Wireless-extension section (report only)

See `configs/site-a-kerberos-summary.md` for the full task-by-task Kerberos build record, and `configs/site-a-network-config.md` for the networking layer.
