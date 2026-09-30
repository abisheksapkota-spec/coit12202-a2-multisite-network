# Network Addressing Scheme

## Site Allocation

| Site | Owner | Supernet |
|---|---|---|
| Site A | Abishek Sapkota | `10.1.0.0/16` |
| Site B | Anima | `10.2.0.0/16` |
| Site C | Umesh | `10.3.0.0/16` |

Site A uses two OPNsense firewalls (edge and internal). Sites B and C use one OPNsense firewall each.

---

## Network Zones

| Zone | Site A | Site B | Site C |
|---|---|---|---|
| WAN / Transit | `10.1.9.0/24` | `10.2.9.0/24` | `10.3.9.0/24` |
| Inter-Firewall | `10.1.8.0/24` | N/A | N/A |
| LAN | `10.1.10.0/24` | `10.2.10.0/24` | `10.3.10.0/24` |
| DMZ | `10.1.20.0/24` | `10.2.20.0/24` | `10.3.20.0/24` |

There is currently no separate management (MGMT) zone.

---

## OPNsense Addressing

| Role | Site A OPNsense1 | Site A OPNsense2 | Site B/C OPNsense |
|---|---|---|---|
| WAN | `10.1.9.1/24` | N/A | `10.X.9.1/24` |
| LAN | `10.1.8.1/24` | `10.1.10.1/24` | `10.X.10.1/24` |
| OPT1 (DMZ) | N/A | `10.1.20.1/24` | `10.X.20.1/24` |

For Site B, `X = 2`.

Therefore, Site B uses:

- OPNsense-B WAN: `10.2.9.1/24`
- OPNsense-B LAN: `10.2.10.1/24`
- OPNsense-B DMZ: `10.2.20.1/24`

---

## Kerberos Hosts

| Site | Realm | KDC | Server | Client |
|---|---|---|---|---|
| Site A | `SITEA.LOCAL` | `10.1.10.10` | `10.1.10.20` | `10.1.10.30` |
| Site B | `SITEB.LOCAL` | `10.2.10.10` | `10.2.10.20` | `10.2.10.30` |
| Site C | `SITEC.LOCAL` | `10.3.10.10` | `10.3.10.20` | `10.3.10.30` |

---

## Site B Hosts

| Host | IP Address | Zone | Purpose |
|---|---|---|---|
| `kdc-B` | `10.2.10.10/24` | LAN | Kerberos KDC and PKI CA |
| `server-B` | `10.2.10.20/24` | LAN | Kerberos application server |
| `client-B` | `10.2.10.30/24` | LAN | Client testing |
| `Linux-B` | `10.2.10.40/24` | LAN | Week 3 and Week 4 security testing |
| `web-B` | `10.2.20.10/24` | DMZ | HTTPS web server |
| `IDS-B` | `10.2.20.20/24` | DMZ | IDS / Suricata |

Site B LAN default gateway:

`10.2.10.1`

Site B DMZ default gateway:

`10.2.20.1`

---

## Site-to-Site VPN

The site-to-site VPN terminates on each site's OPNsense WAN interface.

- VPN Type: IPsec
- IKE Version: IKEv2
- VPN Mode: Policy-based
- Authentication: Pre-Shared Key (PSK)
- Site B Local Network: `10.2.0.0/16`
- Site A Remote Network: `10.1.0.0/16`

Pre-shared keys are shared securely between group members and must not be stored in the GitHub repository.

---

## Tailscale

Each site advertises its complete `/16` network through its VPNRouter.

| Site | Advertised Route |
|---|---|
| Site A | `10.1.0.0/16` |
| Site B | `10.2.0.0/16` |
| Site C | `10.3.0.0/16` |

Advertised routes require approval through the Tailscale administration console.

Site B VPNRouter advertises:

`10.2.0.0/16`

---

## Site B PKI / HTTPS

Site B uses the following PKI hierarchy:

`Root CA → Intermediate CA → web-B Server Certificate`

The Certificate Authority configuration is performed on `kdc-B`.

The HTTPS server configuration is:

- Host: `web-B`
- IP Address: `10.2.20.10`
- HTTPS Port: `443`
- Certificate Hostname: `web-b.siteb.lab`

The `web-B` server certificate is signed by the Site B Intermediate CA.

Nginx on `web-B` is configured to use the signed certificate and HTTPS port `443`.

---

## Site B Security Testing Host

`Linux-B` is connected to the Site B LAN for Week 3 and Week 4 security activities.

- Hostname: `Linux-B`
- IP Address: `10.2.10.40/24`
- Default Gateway: `10.2.10.1`
- Network: `10.2.10.0/24`

`Linux-B` is used for password security, password hashing, authentication, PAM policy, SSH security, and related security testing.

---

## Security Notes

Sensitive authentication information must not be committed to the repository.

The following information must remain private:

- IPsec pre-shared keys
- User passwords
- Private certificate keys
- SSH private keys
- Kerberos passwords and secrets
- Tailscale authentication links or authentication keys
