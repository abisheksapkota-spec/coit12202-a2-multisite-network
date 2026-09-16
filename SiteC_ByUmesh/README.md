# Site C – Umesh

## COIT12202 Assessment 2 – Secured Multi-Site Network

**Site Owner:** Umesh  
**Site:** Site C  
**Kerberos Realm:** `SITEC.LOCAL`  
**Site Supernet:** `10.3.0.0/16`

---

## 1. Site C Overview

Site C is one component of the secured multi-site network developed for the COIT12202 Assessment 2 team project.

Site C uses a single OPNsense firewall to provide network segmentation and firewall control between the WAN, LAN, and DMZ networks.

The site also includes Kerberos authentication, IPsec site-to-site VPN connectivity, Tailscale routing, and Suricata IDS functionality.

---

## 2. Network Addressing

| Device / Network | IP Address / Subnet |
|---|---|
| Site C Supernet | `10.3.0.0/16` |
| WAN / Transit Network | `10.3.9.0/24` |
| OPNsense-C WAN | `10.3.9.1/24` |
| VPNRouter-C Transit | `10.3.9.2` |
| LAN Network | `10.3.10.0/24` |
| OPNsense-C LAN | `10.3.10.1/24` |
| kdc-C | `10.3.10.10` |
| server-C | `10.3.10.20` |
| client-C | `10.3.10.30` |
| Host1-C | `10.3.10.40` |
| DMZ Network | `10.3.20.0/24` |
| OPNsense-C OPT1 / DMZ | `10.3.20.1/24` |
| web-C | `10.3.20.10` |
| IDS-C | `10.3.20.20` |

---

## 3. OPNsense Configuration

OPNsense-C provides firewalling and routing for Site C.

### Interface Assignment

- WAN → `vtnet0`
- LAN → `vtnet1`
- OPT1 / DMZ → `vtnet2`

### Interface Addresses

- WAN → `10.3.9.1/24`
- LAN → `10.3.10.1/24`
- OPT1 → `10.3.20.1/24`

### WAN Gateway

VPNRouter-C is configured as the WAN gateway:

`10.3.9.2`

The WAN private-network and bogon-network automatic blocking options were disabled for the GNS3 lab environment.

---

## 4. Firewall Configuration

### WAN Rule

A WAN Pass rule was configured to permit traffic from the Site C transit network.

- Action: `Pass`
- Protocol: `Any`
- Source: `10.3.9.0/24`
- Destination: `Any`

### OPT1 / DMZ Rule

A Pass rule was configured for the Site C DMZ.

- Action: `Pass`
- Protocol: `Any`
- Source: `OPT1 net`
- Destination: `Any`

Network aliases were also created for:

- `SITE_C_LAN` → `10.3.10.0/24`
- `SITE_C_DMZ` → `10.3.20.0/24`

---

## 5. IPsec VPN

Site C uses IKEv2 IPsec for secured site-to-site VPN connectivity.

OPNsense-C WAN (`10.3.9.1`) is the IPsec termination point.

### Site C ↔ Site A

Remote Gateway:

`10.1.9.1`

### Site C ↔ Site B

Remote Gateway:

`10.2.9.1`

### Phase 1 Parameters

- Key Exchange: `IKEv2`
- Encryption: `AES-GCM`
- Key Length: `256 bits`
- ICV: `128 bits`
- Hash: `SHA256`
- DH Group: `14 (2048 bit)`
- Lifetime: `28800`
- Authentication: Pre-Shared Key

> Pre-shared keys are intentionally not stored in this repository.

Phase 1 has been configured for both Site A and Site B.

Phase 2 and end-to-end tunnel verification will be completed in coordination with the peer sites.

---

## 6. Tailscale

Tailscale will run on VPNRouter-C and provide connectivity between the three site networks.

Site C will advertise:

`10.3.0.0/16`

Planned command:

`tailscale up --advertise-routes=10.3.0.0/16 --snat-subnet-routes=false --accept-routes`

The advertised subnet route must also be approved through the team's Tailscale administration console.

**Status:** Pending configuration and cross-site testing.

---

## 7. Kerberos

Site C Kerberos realm:

`SITEC.LOCAL`

Main Kerberos systems:

- kdc-C → `10.3.10.10`
- server-C → `10.3.10.20`
- client-C → `10.3.10.30`

The final configuration will include local authentication and cross-realm trust with the other project sites.

**Status:** Pending final configuration and cross-site authentication testing.

---

## 8. Suricata IDS

IDS-C is used for intrusion detection on Site C.

Planned IDS testing includes:

- Suspicious HTTP User-Agent detection
- TCP port 4444 / possible reverse-shell traffic detection
- Suricata alert-log verification
- Positive and negative test evidence

**Status:** Pending final configuration and alert demonstration.

---

## 9. Evidence

Site C evidence screenshots are stored in:

`SiteC_ByUmesh/Images/`

Evidence includes:

- GNS3 Site C topology
- OPNsense interface assignment
- OPNsense interface IP configuration
- WAN gateway configuration
- WAN firewall rule
- OPT1 / DMZ firewall rule
- web-C IP configuration
- Local connectivity testing
- IPsec Phase 1 configuration

Additional evidence will be added for:

- Tailscale connectivity
- IPsec Phase 2
- Cross-site VPN connectivity
- Kerberos authentication
- Cross-realm authentication
- Suricata alerts

---

## 10. Current Progress

### Done

- [x] Build Site C GNS3 topology
- [x] Assign OPNsense-C interfaces
- [x] Configure WAN `10.3.9.1/24`
- [x] Configure LAN `10.3.10.1/24`
- [x] Configure OPT1 `10.3.20.1/24`
- [x] Configure WAN gateway `10.3.9.2`
- [x] Disable WAN private-network and bogon blocking
- [x] Create Site C network aliases
- [x] Configure WAN firewall Pass rule
- [x] Configure OPT1 / DMZ firewall Pass rule
- [x] Configure web-C addressing
- [x] Verify OPNsense-C to web-C connectivity
- [x] Configure IPsec Phase 1 for Site A
- [x] Configure IPsec Phase 1 for Site B
- [x] Enable IPsec on OPNsense-C

### In Progress

- [ ] Complete IPsec configuration with peer sites
- [ ] Organise Site C assessment evidence

### To Do

- [ ] Configure Tailscale on VPNRouter-C
- [ ] Advertise and approve `10.3.0.0/16`
- [ ] Configure IPsec Phase 2
- [ ] Verify Site C ↔ Site A VPN
- [ ] Verify Site C ↔ Site B VPN
- [ ] Capture VPN before/after evidence
- [ ] Configure Site C Kerberos KDC
- [ ] Configure server-C and client-C
- [ ] Configure cross-realm Kerberos trust
- [ ] Demonstrate cross-site Kerberos authentication
- [ ] Configure IDS-C / Suricata
- [ ] Demonstrate Suricata alerts
- [ ] Add Site C `.gns3project` export
- [ ] Upload final evidence screenshots
- [ ] Complete Site C report contributions
- [ ] Prepare Site C final video demonstration

---

## 11. Repository Structure

```text
SiteC_ByUmesh/
├── README.md
├── SiteC-Umesh.gns3project
├── configs/
│   ├── OPNsense/
│   ├── Kerberos/
│   └── IDS/
└── Images/
