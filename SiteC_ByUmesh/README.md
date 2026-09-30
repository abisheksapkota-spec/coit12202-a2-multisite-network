# Site C – Umesh Kunwar

## COIT12202 Assessment 2 – Secured Multi-Site Network

**Owner:** Umesh Kunwar  
**Site:** Site C  
**Kerberos Realm:** `SITEC.LOCAL`  
**Site Supernet:** `10.3.0.0/16`

---

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

---

## OPNsense-C

### Interfaces

- WAN `vtnet0` → `10.3.9.1/24`
- LAN `vtnet1` → `10.3.10.1/24`
- OPT1/DMZ `vtnet2` → `10.3.20.1/24`
- WAN gateway → `10.3.9.2`

### Firewall Configuration

- WAN permits Site A transit source `10.1.9.0/24`
- WAN permits Site B transit source `10.2.9.0/24`
- Unrestricted wildcard WAN pass rule removed
- LAN IPv4 LAN-net-to-any rule retained
- OPT1/DMZ pass rule configured
- IPsec pass rule configured
- Aliases `SITE_C_LAN` and `SITE_C_DMZ` configured

---

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

Site C successfully establishes the IPsec tunnel and sends encrypted traffic toward Site A. Final bidirectional LAN-to-LAN verification remains dependent on successful return traffic from the remote site.

### Site C ↔ Site B

- Peer WAN: `10.2.9.1`
- Local network: `10.3.0.0/16`
- Remote network: `10.2.0.0/16`
- Phase 1 and Phase 2 configuration completed

---

## Tailscale VPN

Site C VPNRouter-C is configured as a Tailscale subnet router.

Advertised Site C subnet:

`10.3.0.0/16`

Completed:

- VPNRouter-C successfully connected to Tailscale
- Site C subnet `10.3.0.0/16` advertised
- Site C subnet route approved in the Tailscale Admin Console
- Tailscale peer connectivity verified
- Site C Tailscale configuration and connectivity evidence collected

Tailscale provides an additional secure overlay mechanism for inter-site connectivity.

---

## Kerberos Authentication

**Realm:** `SITEC.LOCAL`

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

Cross-realm Kerberos authentication requires coordination with another site's Kerberos realm and remains a group/cross-site integration task.

---

## Suricata IDS

**IDS-C:** `10.3.20.20`

Completed:

- Suricata installed and configured
- Suricata configuration validation successful
- Custom IDS rules successfully loaded
- Suricata IDS service started
- ICMP detection tested
- Fresh `SITE-C ICMP Ping Detected` alerts captured in `fast.log`
- Suricata alert evidence collected

Suricata provides IDS monitoring for Site C and demonstrates detection of network activity using custom signatures.

---

## HTTPS and Certificate Security

HTTPS security was implemented for the Site C DMZ web server.

**Web server:** `web-C`  
**IP address:** `10.3.20.10`  
**HTTPS port:** `443`

Completed:

- Created a Site C Certificate Authority (CA)
- Generated a private key for `web-C`
- Generated a Certificate Signing Request (CSR)
- Issued a CA-signed certificate for `web-C`
- Configured Subject Alternative Names (SANs) for:
  - `web-C`
  - `web-c.sitec.local`
  - `10.3.20.10`
- Configured Nginx as the HTTPS web server
- Configured Nginx to listen on TCP port `443`
- Configured the issued certificate and private key in Nginx
- Nginx configuration syntax successfully validated
- HTTPS service successfully started
- TCP port `443` verified as listening
- Server certificate successfully verified against the Site C CA
- TLS certificate verification returned successfully

This configuration demonstrates certificate-based server authentication and encrypted HTTPS communication for the Site C DMZ web service.

---

## SSH Hardening

SSH security hardening was implemented on:

**server-C:** `10.3.10.20`

OpenSSH was installed and configured with stronger remote-access controls.

### SSH Security Controls

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
- Created the user's `.ssh` directory with restricted permissions
- Generated an Ed25519 SSH key pair
- Configured `authorized_keys`
- Applied appropriate file ownership and permissions
- SSH configuration validated using `sshd -t`
- SSH daemon successfully started/reloaded
- Public-key SSH login successfully demonstrated
- `whoami` confirmed successful login as `umesh`
- Password-only authentication was tested and rejected
- Root password-based SSH authentication was rejected

This demonstrates that Site C administrative SSH access is restricted to key-based authentication and direct root SSH access is disabled.

---

## Password Management and Account Security

Password and account security controls were implemented on `server-C`.

Completed:

- Created a dedicated non-root account `umesh`
- Configured the user with a strong password
- Password stored as a cryptographic hash rather than plaintext
- SHA-512 password hashing demonstrated
- User account ownership and home-directory permissions verified
- `.ssh` directory permissions restricted
- `authorized_keys` permissions restricted
- SSH password authentication disabled
- Public-key authentication required for remote SSH access

The password itself is not stored in the repository or screenshots.

---

## Defence-in-Depth – Site C

Site C implements multiple complementary security controls:

1. **OPNsense firewall** – separates WAN, LAN and DMZ security zones and enforces traffic rules.
2. **IPsec VPN** – provides encrypted site-to-site communication.
3. **Tailscale** – provides an additional WireGuard-based secure overlay and subnet routing mechanism.
4. **Kerberos** – provides centralized authentication for Site C systems.
5. **Suricata IDS** – monitors traffic and generates alerts for matching network activity.
6. **HTTPS/TLS** – protects web traffic and provides certificate-based server authentication.
7. **SSH hardening** – restricts administrative access using public-key authentication and disables direct root/password SSH access.
8. **Password/account security** – protects local credentials and reduces exposure of privileged accounts.

Together, these controls demonstrate a layered security architecture rather than relying on a single defensive mechanism.

---

## GNS3 Project

The final Site C GNS3 topology has been saved in the repository as:

`SiteC_Umesh_Final.gns3`

The topology includes:

- OPNsense-C firewall
- VPNRouter-C
- LAN switch
- Host1-C
- kdc-C
- server-C
- client-C
- DMZ switch
- web-C
- Hub-C
- IDS-C

The topology demonstrates Site C's firewall, VPN, authentication, web security, server hardening and IDS components.

---

## Project Status

### Completed – Site C

- Site C GNS3 topology
- Site C addressing
- OPNsense WAN/LAN/DMZ configuration
- WAN gateway configuration
- LAN and DMZ firewall rules
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
- Tailscale subnet route approval
- Tailscale connectivity evidence
- Kerberos KDC configuration
- Kerberos principals
- client-C Kerberos authentication
- server-C keytab authentication
- Suricata IDS configuration
- Suricata custom rules
- Suricata fresh alert evidence
- HTTPS web server configuration
- Site C CA and server certificate
- HTTPS certificate verification
- Nginx TLS configuration
- SSH server installation and configuration
- SSH hardening
- Ed25519 public-key authentication
- Successful SSH key-based login
- Password authentication rejection evidence
- Root SSH login disabled
- Password/account security configuration
- SHA-512 password hashing
- Final Site C topology evidence
- Final GNS3 project file
- Site C assessment evidence collection

### Remaining Cross-Site / Group Verification

- Final bidirectional cross-site LAN-to-LAN verification
- Site C ↔ Site B cross-site traffic verification
- Cross-realm Kerberos trust and authentication
- Required cross-site before/after packet-capture evidence
- Group report integration
- Multi-site architecture/diagram integration
- Final group demonstration/video
- Final repository and submission verification

---

## Repository Structure

```text
SiteC_ByUmesh/
├── README.md
├── ADDRESSING.md
├── SiteC_Umesh_Final.gns3
└── SITE C IMAGES/
    └── Site C assessment evidence screenshots
```

---

## Security Note

Passwords, private SSH keys, CA private keys, IPsec PSKs, Kerberos keytabs and other authentication secrets must **not** be committed to the GitHub repository.

Only appropriate configuration documentation, public certificates/public keys and assessment evidence should be stored in the repository.
