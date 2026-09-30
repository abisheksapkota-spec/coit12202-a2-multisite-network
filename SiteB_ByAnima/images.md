[Images.md](https://github.com/user-attachments/files/32838319/Images.md)

# Site B - Screenshot Evidence

**Site:** B (Anima)\
**Network:** `10.2.0.0/16`

This file indexes the Site B screenshots stored in the `Images/` folder.
The evidence covers the Site B topology, OPNsense addressing and
firewall configuration, Kerberos, Tailscale, IPsec, IDS/Suricata,
PKI/HTTPS, and Week 3/4 security work.

> **Note:** Keep the image files inside the repository's `Images/`
> folder so the links below work.

------------------------------------------------------------------------

## 1. Site B Network Topology

![Site B Network Topology](Images/Network%20topology.png)

Shows the integrated Site B GNS3 topology, including OPNsense-B,
VPNRouter-B, LAN hosts, DMZ hosts, web-B and IDS-B.

------------------------------------------------------------------------

## 2. OPNsense-B Interface Addressing

![OPNsense-B Interface
Addressing](Images/01-OPNsense-B-Interface-Addressing.png)

Shows the configured OPNsense-B interfaces and Site B addressing.

------------------------------------------------------------------------

## 3. OPNsense-B WAN Gateway

![OPNsense-B WAN Gateway](Images/OPNsense-B-WAN-Gateway-Verified..png)

Shows the Site B WAN gateway configuration/verification.

------------------------------------------------------------------------

## 4. OPNsense-B WAN Firewall Rule

![OPNsense-B WAN Firewall
Rule](Images/07-OPNsense-B-WAN-Firewall-Rule.png)

Shows the configured WAN firewall rule on OPNsense-B.

------------------------------------------------------------------------

## 5. OPNsense-B WAN Firewall Rules Applied

![OPNsense-B WAN Firewall Rules
Applied](Images/08-OPNsense-B-WAN-Firewall-Rules-Applied.png)

Shows the applied OPNsense-B WAN firewall rules.

------------------------------------------------------------------------

## 6. Private/Bogon Blocking Setting

![OPNsense-B Private Bogon
Setting](Images/22-OPNsense-B-WAN-Private-Bogon-Disabled..png)

Shows the WAN private/bogon blocking setting used for the Site B lab
environment.

------------------------------------------------------------------------

## 7. OPNsense-B Interface Connectivity

![OPNsense-B Interface Ping
Test](Images/17-OPNsense-B-Interface-Ping-Test.png)

Shows connectivity testing across the configured OPNsense-B interfaces.

------------------------------------------------------------------------

## 8. OPNsense-B Internet Connectivity

![OPNsense-B Internet
Connectivity](Images/OPNsense-B-Internet-Connectivity.png)

Shows successful external connectivity from OPNsense-B.

------------------------------------------------------------------------

## 9. VPNRouter-B Internet Connectivity

![VPNRouter-B Internet
Connectivity](Images/-VPNRouter-B-Internet-Connectivity.png)

Shows successful Internet connectivity from VPNRouter-B.

------------------------------------------------------------------------

## 10. Tailscale Site B Connection

![Tailscale Site B
Connected](Images/07-Tailscale-SiteB-Connected.png.png)

Shows VPNRouter-B connected to the Site B Tailscale environment.

------------------------------------------------------------------------

## 11. Tailscale Site B Advertised Route

![Tailscale Site B Advertised
Route](Images/10-Tailscale-SiteB-Advertised-Route.png.png)

Shows the Site B route advertisement for `10.2.0.0/16`.

------------------------------------------------------------------------

## 12. Site B IPsec

![OPNsense-B IPsec
Established](Images/OPNsense-B-IPsec-Established-Installed.png)

Shows the Site B IPsec configuration/status evidence.

------------------------------------------------------------------------

# Kerberos Evidence

## 13. Site B Kerberos Realm Configuration

![Kerberos Realm
Configuration](Images/02-Kerberos-SiteB-Realm-Configuration..png)

Shows the Site B Kerberos realm configuration.

------------------------------------------------------------------------

## 14. Site B KDC Database

![Kerberos KDC Database](Images/03-Kerberos-SiteB-KDC-Database.png)

Shows creation/configuration of the Site B Kerberos KDC database.

------------------------------------------------------------------------

## 15. Site B Kerberos Principals

![Kerberos Principals](Images/04-Kerberos-SiteB-Principals.png)

Shows the Kerberos principals configured for Site B.

------------------------------------------------------------------------

## 16. Site B Server Keytab

![Kerberos Server Keytab](Images/05-Kerberos-SiteB-Server-Keytab%20.png)

Shows the Site B server keytab configuration.

------------------------------------------------------------------------

## 17. Site B Server Keytab Installed

![Kerberos Server Keytab
Installed](Images/Kerberos-SiteB-Server-Keytab-Installed.png)

Shows the server keytab installed on the Site B server.

------------------------------------------------------------------------

## 18. Site B Client Ticket

![Kerberos Client Ticket](Images/06-Kerberos-SiteB-Client-Ticket.png)

Shows Kerberos ticket acquisition from the Site B client.

------------------------------------------------------------------------

## 19. Site B Client Ticket Verified

![Kerberos Client Ticket
Verified](Images/Kerberos-SiteB-Client-Ticket-Verified.png)

Shows verification of the Site B Kerberos client ticket.

------------------------------------------------------------------------

## 20. Site B Service Ticket

![Kerberos Service Ticket](Images/Kerberos-SiteB-Service-Ticket.png.png)

Shows the Kerberos service ticket used for the Site B service.

------------------------------------------------------------------------

## 21. Server-B GSSAPI SSH

![Server-B GSSAPI SSH](Images/Server-B-GSSAPI-SSH-Enabled.png)

Shows GSSAPI SSH configuration/evidence on Server-B.

------------------------------------------------------------------------

## 22. Server-B OpenSSH

![Server-B OpenSSH](Images/Server-B-OpenSSH-Running..png)

Shows OpenSSH evidence from Server-B.

------------------------------------------------------------------------

## 23. Server-B Internet Connectivity

![Server-B Internet
Connectivity](Images/Server-B-Internet-Connectivity.png.png)

Shows network connectivity from Server-B.

------------------------------------------------------------------------

# IDS / Suricata Evidence

## 24. IDS-B Management IP

![IDS-B Management IP](Images/08-IDS-B-Management-IP.png)

Shows the IDS-B management/interface IP configuration.

------------------------------------------------------------------------

## 25. IDS-B Routing Configuration

![IDS-B Routing
Configuration](Images/09-IDS-B-Routing-Configuration.png)

Shows routing configuration on IDS-B.

------------------------------------------------------------------------

## 26. OPNsense-B to IDS-B Connectivity

![OPNsense-B to IDS-B
Ping](Images/18-OPNsense-B-to-IDS-B-Ping-Success.png%20.png)

Shows successful connectivity from OPNsense-B to IDS-B.

------------------------------------------------------------------------

## 27. IDS-B to OPNsense-B Connectivity

![IDS-B to OPNsense-B
Ping](Images/19-IDS-B-to-OPNsense-B-Ping-Success..png)

Shows successful connectivity from IDS-B to OPNsense-B.

------------------------------------------------------------------------

## 28. Suricata-B Configuration

![Suricata-B Configuration](Images/15-Suricata-B-Configuration..png)

Shows the Suricata configuration used on IDS-B.

------------------------------------------------------------------------

## 29. Suricata-B Alert Detection

![Suricata-B Alert
Detection](Images/16-Suricata-B-Alert-Detection.png%20%23U2705.png)

Shows Suricata generating/detecting an alert.

------------------------------------------------------------------------

## 30. Suricata-B Real Traffic Detection

![Suricata-B Real Traffic
Detection](Images/Suricata-B-Real-Traffic-Detection.png%20.png)

Shows Suricata observing real Site B network traffic.

------------------------------------------------------------------------

# Week 2 - PKI / HTTPS Evidence

## 31. KDC-B OpenSSL

![KDC-B OpenSSL](Images/Week2-PKI-01-KDC-B-OpenSSL.png)

Shows OpenSSL available on KDC-B for Site B PKI work.

------------------------------------------------------------------------

## 32. Site B Root CA

![Site B Root CA](Images/Week2-PKI-02-Root-CA-Created..png)

Shows creation/verification of the Site B Root CA.

------------------------------------------------------------------------

## 33. Site B Intermediate CA

![Site B Intermediate
CA](Images/Week2-PKI-03-Intermediate-CA-Verified..png)

Shows successful verification of the Site B Intermediate CA.

------------------------------------------------------------------------

## 34. web-B Certificate Signing Request

![web-B CSR](Images/Week2-PKI-04-Web-B-CSR-Created.png)

Shows the CSR generated for `web-b.siteb.lab`.

------------------------------------------------------------------------

## 35. web-B Certificate Files

![web-B Certificate
Files](Images/Week2-PKI-Web-B-Certificate-Files..png)

Shows the certificate, CA chain and private key files available on
web-B.

------------------------------------------------------------------------

## 36. Nginx HTTPS Configuration Test

![Nginx HTTPS Success](Images/Week2-Nginx-HTTPS-Success..png)

Shows the successful Nginx HTTPS configuration test.

------------------------------------------------------------------------

## 37. web-B HTTPS Port 443

![web-B HTTPS Port
443](Images/Week2-PKI-Web-B-HTTPS-Port-443-Listening.png)

Shows HTTPS port `443` listening on web-B.

------------------------------------------------------------------------

## 38. Client-B to web-B Connectivity

![Client-B to web-B Ping](Images/Client-B-to-Web-B-Ping-Success.png)

Shows successful network connectivity from Client-B to web-B.

------------------------------------------------------------------------

# Week 3 / Week 4 Evidence

## 39. Linux-B Connectivity

![Linux-B Connectivity](Images/Week3-4-Linux-B-IP-Connectivity.png)

Shows the additional Linux security-testing host connected to the Site B
LAN with its assigned address/connectivity.

------------------------------------------------------------------------

## 40. Ed25519 SSH Key Generation

![Ed25519 SSH Key](Images/Week4-SSH-01-Ed25519-Key-Generated.png)

Shows generation of the Ed25519 public/private key pair for the Week 4
SSH security activity.

------------------------------------------------------------------------

# Evidence Status

The screenshots above document the Site B implementation completed so
far. Additional Week 3 password-policy and Week 4 SSH-hardening
screenshots should be added to the `Images/` folder and referenced here
after those activities are completed.

## Security Reminder

Do not upload any of the following to the repository:

-   Passwords
-   IPsec pre-shared keys
-   SSH private keys
-   Certificate private keys
-   Kerberos secrets
-   Tailscale authentication keys or login links
