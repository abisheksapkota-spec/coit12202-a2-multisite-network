# Site C – Umesh Kunwar

## COIT12202 Assessment 2 – Secured Multi-Site Network

**Owner:** Umesh Kunwar  
**Site:** Site C  
**Kerberos Realm:** `SITEC.LOCAL`  
**Site Supernet:** `10.3.0.0/16`

---

## 1. Site C Overview

Site C is one component of the COIT12202 Assessment 2 secured multi-site network.

The Site C implementation uses a defence-in-depth approach incorporating:

- OPNsense firewalling and network segmentation
- IPsec site-to-site VPN configuration
- Tailscale secure overlay networking
- Kerberos authentication
- Suricata intrusion detection
- HTTPS/TLS web-server protection
- SSH hardening
- Password and account security

The Site C network uses the supernet `10.3.0.0/16`.

---

## 2. Addressing

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

---

## 3. OPNsense-C Firewall

### Interfaces

- WAN `vtnet0` → `10.3.9.1/24`
- LAN `vtnet1` → `10.3.10.1/24`
- OPT1/DMZ `vtnet2` → `10.3.20.1/24`
- WAN gateway → `10.3.9.2`

### Firewall Configuration

Completed:

- WAN permits Site A transit source `10.1.9.0/24`
- WAN permits Site B transit source `10.2.9.0/24`
- Unrestricted wildcard WAN pass rule removed
- LAN IPv4 LAN-net-to-any rule retained
- OPT1/DMZ pass rule configured
- IPsec pass rule configured
- Alias `SITE_C_LAN` configured
- Alias `SITE_C_DMZ` configured

OPNsense provides the primary traffic-control and segmentation layer for Site C.

---

## 4. IPsec VPN

Site C terminates IPsec on OPNsense-C WAN `10.3.9.1`.

### Site C ↔ Site A

- Peer WAN: `10.1.9.1`
- Local network: `10.3.0.0/16`
- Remote network: `10.1.0.0/16`
- IKEv2 tunnel successfully established
- CHILD_SA successfully installed
- IPsec SPD policies verified
- Encrypted ESP traffic from Site C captured

Site C successfully establishes the IPsec tunnel and sends encrypted traffic toward Site A.

Final bidirectional LAN-to-LAN verification remains dependent on successful return traffic from the remote site.

### Site C ↔ Site B

- Peer WAN: `10.2.9.1`
- Local network: `10.3.0.0/16`
- Remote network: `10.2.0.0/16`
- Phase 1 configuration completed
- Phase 2 configuration completed

Final cross-site traffic verification remains an integration task.

---

## 5. Tailscale VPN

VPNRouter-C is configured as the Site C Tailscale subnet router.

### Advertised Subnet

`10.3.0.0/16`

Completed:

- VPNRouter-C successfully connected to Tailscale
- Site C subnet `10.3.0.0/16` advertised
- Site C subnet route approved in the Tailscale Admin Console
- Tailscale peer connectivity verified
- Site C Tailscale configuration and connectivity evidence collected

Tailscale provides an additional secure overlay mechanism for multi-site connectivity.

---

## 6. Kerberos Authentication

**Realm:** `SITEC.LOCAL`

| Component | Address |
|---|---|
| KDC | `10.3.10.10` |
| Server | `10.3.10.20` |
| Client | `10.3.10.30` |

Completed:

- KDC database created for `SITEC.LOCAL`
- User principal created
- Server host principal created
- Server keytab generated
- Kerberos KDC service started successfully
- KDC authentication verified using `kinit` and `klist`
- client-C successfully obtained a Ticket Granting Ticket
- server-C successfully authenticated using its host keytab

Local Site C Kerberos authentication is complete.

Cross-realm Kerberos authentication requires coordination with the Kerberos realms at the other sites and remains a group integration task.

---

## 7. Suricata IDS

**IDS-C:** `10.3.20.20`

Completed:

- Suricata installed and configured
- Configuration validation successful
- Custom IDS rules loaded
- Suricata IDS process started
- ICMP detection tested
- Fresh `SITE-C ICMP Ping Detected` alerts generated
- Alerts verified in `fast.log`
- Suricata evidence collected

Suricata provides an additional monitoring and detection layer for Site C.

A live ICMP test is used in the final demonstration to generate a fresh Suricata alert.

---

## 8. HTTPS and Certificate Security

HTTPS protection was implemented for the Site C DMZ web server.

**Web server:** `web-C`  
**IP address:** `10.3.20.10`  
**HTTPS port:** `443`

Completed:

- Created a Site C Certificate Authority
- Generated a private key for `web-C`
- Generated a Certificate Signing Request
- Issued a CA-signed certificate for `web-C`
- Configured Subject Alternative Names for:
  - `web-C`
  - `web-c.sitec.local`
  - `10.3.20.10`
- Configured Nginx as the HTTPS server
- Configured Nginx to listen on TCP port `443`
- Configured the issued certificate and private key in Nginx
- Nginx configuration successfully validated
- HTTPS service successfully started
- TCP port `443` verified as listening
- Server certificate verified against the Site C CA
- TLS certificate verification completed successfully

This provides encrypted HTTPS communication and certificate-based server authentication for the Site C DMZ web service.

---

## 9. SSH Hardening

SSH security hardening was implemented on:

**server-C:** `10.3.10.20`

### Security Controls

- `PermitRootLogin no`
- `PubkeyAuthentication yes`
- `PasswordAuthentication no`
- `PermitEmptyPasswords no`
- `MaxAuthTries 3`
- `X11Forwarding no`
- `AllowTcpForwarding no`
- `ClientAliveInterval 300`
- `ClientAliveCountMax 2`

Additional configuration completed:

- Created non-root SSH user `umesh`
- Created `.ssh` directory with restricted permissions
- Generated an Ed25519 SSH key pair
- Configured `authorized_keys`
- Applied appropriate file ownership and permissions
- SSH configuration validated using `sshd -t`
- SSH daemon successfully started/reloaded
- Public-key SSH login successfully demonstrated
- `whoami` confirmed successful login as `umesh`
- Password-only authentication tested and rejected
- Root password-based SSH authentication rejected

Site C administrative SSH access is therefore restricted to public-key authentication and direct root SSH access is disabled.

---

## 10. Password Management and Account Security

Password and account security controls were implemented on `server-C`.

Completed:

- Dedicated non-root account `umesh` created
- Password configured for the local account
- Password stored as a cryptographic hash rather than plaintext
- SHA-512 password hashing demonstrated
- User account ownership verified
- Home-directory permissions verified
- `.ssh` directory permissions restricted
- `authorized_keys` permissions restricted
- SSH password authentication disabled
- Public-key authentication required for remote SSH access

Passwords are not stored in the repository or assessment screenshots.

---

## 11. Defence-in-Depth – Site C

Site C uses multiple complementary security controls:

1. **OPNsense firewall** – separates WAN, LAN and DMZ security zones and enforces network-access policies.
2. **IPsec VPN** – provides encrypted site-to-site communication.
3. **Tailscale** – provides an additional secure overlay and subnet-routing mechanism.
4. **Kerberos** – provides centralized authentication.
5. **Suricata IDS** – monitors network traffic and generates security alerts.
6. **HTTPS/TLS** – protects web communication and provides certificate-based server authentication.
7. **SSH hardening** – protects administrative access using public-key authentication.
8. **Password/account security** – protects local credentials and reduces privileged-account exposure.

These controls provide prevention, authentication, encryption, monitoring and detection at different layers of the Site C architecture.

---

## 12. Final Video Demonstration – Site C

The Site C contribution to the final group demonstration focuses on two complementary security layers: **OPNsense firewall enforcement and Suricata intrusion detection**.

### Demonstration Sequence

1. Show the complete Site C GNS3 topology.
2. Show the relevant OPNsense firewall rule.
3. Generate traffic that is not permitted by the firewall policy.
4. Demonstrate that the traffic is blocked.
5. Open OPNsense **Firewall → Log Files → Live View**.
6. Show the corresponding firewall block event.
7. Generate permitted ICMP traffic through Site C.
8. Check the Suricata `fast.log`.
9. Demonstrate a fresh `SITE-C ICMP Ping Detected` alert.
10. Explain how the firewall and IDS provide defence-in-depth.

### Demonstration Security Principle

The OPNsense firewall provides **prevention and policy enforcement**, while Suricata provides **monitoring and detection**.

Together, these controls demonstrate that Site C does not rely on a single security mechanism.

---

## 13. GNS3 Project

The final Site C GNS3 topology is saved in the repository as:

`SiteC_Umesh_Final.gns3`

The topology contains:

- NAT-C
- VPNRouter-C
- OPNsense-C
- LAN switch
- Host1-C
- kdc-C
- server-C
- client-C
- DMZ switch
- web-C
- Hub-C
- IDS-C

The topology demonstrates Site C's firewall, VPN, authentication, web-security, server-hardening and IDS components.

---

## 14. Project Status

### Completed – Site C

- Site C GNS3 topology
- Site C addressing
- OPNsense WAN/LAN/DMZ configuration
- WAN gateway configuration
- LAN and DMZ firewall configuration
- IPsec firewall pass rule
- Site A and Site B WAN peer rules
- web-C configuration and local DMZ connectivity
- IPsec Phase 1 and Phase 2 configuration
- Site A IKEv2 tunnel establishment
- IPsec CHILD_SA installation
- IPsec SPD verification
- ESP encrypted traffic evidence
- Tailscale Site C configuration
- Tailscale subnet advertisement
- Tailscale subnet-route approval
- Tailscale connectivity evidence
- Kerberos KDC configuration
- Kerberos principals
- client-C Kerberos authentication
- server-C keytab authentication
- Suricata IDS configuration
- Suricata custom detection rules
- Suricata fresh ICMP alert evidence
- HTTPS web-server configuration
- Site C CA and server certificate
- HTTPS certificate verification
- Nginx TLS configuration
- SSH server installation and configuration
- SSH hardening
- Ed25519 public-key authentication
- Successful SSH key-based login
- Password-authentication rejection evidence
- Root SSH login disabled
- Password/account security configuration
- SHA-512 password hashing
- Final Site C topology evidence
- Final GNS3 project file
- Site C assessment evidence collection
- Site C video demonstration plan

### Remaining Cross-Site / Group Integration

- Final bidirectional cross-site LAN-to-LAN verification
- Site C ↔ Site B cross-site traffic verification
- Cross-realm Kerberos trust and authentication
- Required cross-site before/after packet-capture evidence
- Group report integration
- Multi-site architecture/diagram integration
- Final group demonstration/video
- Final repository and submission verification

---

## 15. Repository Structure

```text
SiteC_ByUmesh/
├── README.md
├── ADDRESSING.md
├── SiteC_Umesh_Final.gns3
└── SITE C IMAGES/
    └── Site C assessment evidence screenshots
    SiteC_ByUmesh/
└── PACKET CAPTURES/
    └── ClientCtoSiteA.pcap
```

---

## 16. Security Note

The following sensitive information must **not** be committed to the GitHub repository:

- Passwords
- Private SSH keys
- CA private keys
- IPsec pre-shared keys
- Kerberos keytabs
- Authentication secrets

Only appropriate configuration documentation, public certificates/public keys and assessment evidence should be stored in the repository.
