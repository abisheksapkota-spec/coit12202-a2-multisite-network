# Site C – Umesh Kunwar

## COIT12202 Assessment 2 – Secured Multi-Site Network

**Owner:** Umesh Kunwar  
**Site:** Site C  
**Kerberos Realm:** `SITEC.LOCAL`  
**Site Supernet:** `10.3.0.0/16`

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

Firewall configuration:
- WAN permits Site A transit source `10.1.9.0/24`
- WAN permits Site B transit source `10.2.9.0/24`
- Unrestricted wildcard WAN pass rule removed
- LAN IPv4 LAN-net-to-any rule retained
- OPT1/DMZ pass rule configured
- Aliases `SITE_C_LAN` and `SITE_C_DMZ` configured

## IPsec VPN

Site C terminates IPsec on OPNsense-C WAN `10.3.9.1`.

### Site C ↔ Site A

- Peer WAN: `10.1.9.1`
- Local network: `10.3.0.0/16`
- Remote network: `10.1.0.0/16`
- IKEv2 tunnel successfully established
- CHILD_SA successfully installed
- IPsec SPD policies verified
- Encrypted ESP traffic from Site C captured

Site C successfully establishes the tunnel and sends encrypted traffic toward Site A. Final bidirectional LAN-to-LAN verification remains dependent on return traffic from the remote site.

### Site C ↔ Site B

- Peer WAN: `10.2.9.1`
- Local network: `10.3.0.0/16`
- Remote network: `10.2.0.0/16`
- Phase 1 and Phase 2 configuration completed

## Tailscale

Site C is configured to advertise:

`10.3.0.0/16`

Tailscale configuration and connectivity evidence has been collected for Site C.

## Kerberos

Realm: `SITEC.LOCAL`

- KDC: `10.3.10.10`
- Server: `10.3.10.20`
- Client: `10.3.10.30`

Completed local Kerberos configuration and verification:

- KDC database created for `SITEC.LOCAL`
- User principal created
- Server host principal created
- Server keytab generated
- Kerberos KDC service started successfully
- KDC authentication verified using `kinit` and `klist`
- client-C successfully obtained a TGT for `SITEC.LOCAL`
- server-C successfully authenticated using its host keytab

Local Site C Kerberos authentication is complete.

Cross-realm Kerberos authentication requires coordination with another site's Kerberos realm and remains a group/cross-site task.

## Suricata IDS

IDS-C: `10.3.20.20`

Completed:

- Suricata installed and configured
- Configuration validation successful
- Custom rules successfully loaded
- Suricata IDS service started
- ICMP detection tested
- Fresh `SITE-C ICMP Ping Detected` alerts captured in `fast.log`

Suricata configuration and local IDS verification for Site C are complete.

## GNS3 Project

The final Site C GNS3 topology has been saved in the repository as:

`SiteC_Umesh_Final.gns3`

The topology includes the Site C firewall, VPN router, LAN hosts, Kerberos systems, DMZ web server and IDS components.

## Project Status

### Completed – Site C

- Site C GNS3 topology
- Site C addressing
- OPNsense WAN/LAN/DMZ configuration
- WAN gateway configuration
- LAN and DMZ firewall rules
- Site A and Site B WAN peer rules
- web-C configuration and local DMZ connectivity
- IPsec Phase 1 and Phase 2 configuration
- Site A IKEv2 tunnel establishment
- IPsec CHILD_SA installation
- IPsec SPD verification
- ESP encrypted traffic evidence
- Tailscale Site C configuration/evidence
- Kerberos KDC configuration
- Kerberos principals
- client-C Kerberos authentication
- server-C keytab authentication
- Suricata IDS configuration
- Suricata fresh alert evidence
- Final Site C topology evidence
- Final GNS3 project file
- Site C assessment evidence collection

### Remaining Cross-Site / Group Verification

- Final bidirectional IPsec LAN-to-LAN verification
- Site C ↔ Site B cross-site traffic verification
- Cross-realm Kerberos trust and authentication
- Required cross-site packet-capture evidence
- Group report integration
- Final group demonstration/video

## Repository Structure

```text
SiteC_ByUmesh/
├── README.md
├── ADDRESSING.md
├── SiteC_Umesh_Final.gns3
└── SITE C IMAGES/
    └── Site C assessment evidence screenshots
    └── Site C assessment evidence screenshots
    ```

## Security Note

Passwords, IPsec PSKs, Kerberos keytabs and other authentication secrets must not be committed to the GitHub repository.
