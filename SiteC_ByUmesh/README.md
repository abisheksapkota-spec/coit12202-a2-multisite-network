# Site C – Umesh Kunwar

## COIT12202 Assessment 2 – Secured Multi-Site Network

**Owner:** Umesh Kunwar  
**Site:** Site C  
**Kerberos realm:** `SITEC.LOCAL`  
**Site supernet:** `10.3.0.0/16`

## Addressing

| Component | Address |
|---|---|
| Site C supernet | `10.3.0.0/16` |
| WAN / transit | `10.3.9.0/24` |
| OPNsense-C WAN | `10.3.9.1/24` |
| VPNRouter-C transit | `10.3.9.2` |
| LAN | `10.3.10.0/24` |
| OPNsense-C LAN | `10.3.10.1/24` |
| kdc-C | `10.3.10.10` |
| server-C | `10.3.10.20` |
| client-C | `10.3.10.30` |
| Host1-C | `10.3.10.40` |
| DMZ | `10.3.20.0/24` |
| OPNsense-C DMZ | `10.3.20.1/24` |
| web-C | `10.3.20.10` |
| IDS-C | `10.3.20.20` |

## OPNsense-C

Interfaces:
- WAN `vtnet0` → `10.3.9.1/24`
- LAN `vtnet1` → `10.3.10.1/24`
- OPT1/DMZ `vtnet2` → `10.3.20.1/24`
- WAN gateway → `10.3.9.2`

Current firewall work:
- WAN permits Site A transit source `10.1.9.0/24`
- WAN permits Site B transit source `10.2.9.0/24`
- Removed the unrestricted wildcard WAN pass rule
- LAN default IPv4 LAN-net-to-any rule retained
- OPT1/DMZ pass rule: source `OPT1 net` → any
- Aliases: `SITE_C_LAN` and `SITE_C_DMZ`

## IPsec

Site C terminates IPsec on OPNsense-C WAN `10.3.9.1`.

### Site C ↔ Site A
- Peer WAN: `10.1.9.1`
- Phase 2 local: `10.3.0.0/16`
- Phase 2 remote: `10.1.0.0/16`

### Site C ↔ Site B
- Peer WAN: `10.2.9.1`
- Phase 2 local: `10.3.0.0/16`
- Phase 2 remote: `10.2.0.0/16`

Phase 2 proposal used:
- Protocol: ESP
- Encryption: AES-256-GCM (`aes256gcm16`)
- Hash: SHA256
- PFS: Group 14 (2048 bits)
- Lifetime: 3600 seconds

Phase 2 configuration has been entered. Final tunnel status and cross-site traffic still need to be captured as evidence.

## Suricata IDS

IDS-C uses `10.3.20.20`. Suricata configuration validation succeeded and custom rules loaded successfully. ICMP traffic between `web-C (10.3.20.10)` and `IDS-C (10.3.20.20)` generated `SITE-C ICMP Ping Detected` alerts in `fast.log`.

Additional custom rules were added/tested during the IDS configuration work. Evidence is stored under `Images/All_Shared_Screenshots/`.

## Tailscale

Site C must advertise the full Site C supernet:

`10.3.0.0/16`

Tailscale cross-site routing still needs final configuration/approval and verification before marking it complete.

## Kerberos

- Realm: `SITEC.LOCAL`
- KDC: `10.3.10.10`
- Server: `10.3.10.20`
- Client: `10.3.10.30`

Local and cross-realm Kerberos verification should only be marked complete after evidence is captured.

## Project status

### Done
- Site C GNS3 topology
- OPNsense WAN/LAN/DMZ addressing
- WAN gateway `10.3.9.2`
- LAN and DMZ firewall rules
- Corrected WAN peer rules for Site A and Site B
- Removed unrestricted wildcard WAN rule
- web-C addressing and local DMZ connectivity
- IPsec Phase 1 for Site A and Site B
- IPsec Phase 2 configuration for Site A and Site B
- IDS-C / Suricata installation and configuration
- Suricata configuration test
- Suricata ICMP alert demonstration

### In Progress
- IPsec end-to-end tunnel verification with Site A and Site B
- Organising final assessment evidence

### To Do / Verify
- Confirm Phase 1 connected and Phase 2 INSTALLED for both peers
- Capture Site C ↔ Site A cross-site ping evidence
- Capture Site C ↔ Site B cross-site ping evidence
- Configure/approve Tailscale route `10.3.0.0/16`
- Verify Tailscale cross-site reachability
- Complete/verify Kerberos local authentication
- Complete cross-realm trust and cross-site Kerberos authentication
- Add final `.gns3project` export
- Complete Site C report contribution and final video evidence

## Repository structure

```text
SiteC_ByUmesh/
├── README.md
├── ADDRESSING.md
├── Images/
│   ├── assessment evidence images
│   └── All_Shared_Screenshots/
├── configs/
├── Documentation/
└── Notes/
```
SiteC_ByUmesh/
└── SiteC_Umesh_Final.gns3project

Do not commit passwords, PSKs, keytabs, or other secrets to GitHub.

