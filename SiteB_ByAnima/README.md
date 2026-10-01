# Site B — owned by Anima

- Place the **Site B** GNS3 export (`.gns3project`) directly in this folder.
- `configs/` — exported OPNsense config XML, `krb5.conf`, `kdc.conf`, keytabs (redact secrets before committing).
- `Images/` — screenshots as evidence: firewall rule tables, zone setup, KDC/kinit output, Suricata alerts, anything the report references from this site.

**Addressing:** 10.2.0.0/16 (see `ADDRESSING.md` for the full breakdown) · **Realm:** SITEB.LOCAL

# Site B - Week 2 Evidence: CA + HTTPS Server

**Site:** B (Anima) | **Realm/domain:** `SITEB.LOCAL` | **Service host:** `web-b.siteb.lab` (`10.2.20.10`)

This evidence documents the Site B private Certificate Authority setup and HTTPS server configuration. A Root CA and Intermediate CA were created on `kdc-B`, a certificate was issued for the Site B web server, and nginx was configured to serve HTTPS on `web-B`.

## What was done

1. **Root CA created**
   - A Site B Root CA was created on `kdc-B` (`10.2.10.10`).
   - The Root CA certificate uses:
     - `CN=SiteB Root CA`
   - The certificate subject and issuer were verified successfully.
   - Evidence:
     - `Week2-PKI-02-Root-CA-Created.png`

2. **Intermediate CA created and verified**
   - An Intermediate CA key and CSR were generated.
   - The Intermediate CA was signed by the Site B Root CA.
   - Verification was performed using:

     `openssl verify -CAfile /root/ca/certs/root-ca.crt /root/ca/intermediate/certs/intermediate.crt`

   - The verification returned:

     `OK`

   - Evidence:
     - `Week2-PKI-03-Intermediate-CA-Verified.png`

3. **web-B server CSR created**
   - A certificate signing request was created for the Site B web server.
   - Certificate identity:
     - Hostname: `web-b.siteb.lab`
     - IP address: `10.2.20.10`
   - The CSR was configured with the required server certificate extensions and Subject Alternative Name.
   - Evidence:
     - `Week2-PKI-04-Web-B-CSR-Created.png`

4. **web-B certificate signed by the Intermediate CA**
   - The web server CSR was signed using the Site B Intermediate CA.
   - The resulting server certificate was verified against the Root CA and Intermediate CA chain.
   - Verification returned:

     `/tmp/server.crt: OK`

5. **Certificate files transferred to web-B**
   - The following files were placed on `web-B`:
     - `server.key`
     - `server.crt`
     - `ca-chain.crt`
   - Evidence:
     - `Week2-PKI-Web-B-Certificate-Files.png`

6. **nginx configured for HTTPS**
   - nginx was configured to use:
     - `/etc/ssl/private/server.key`
     - `/etc/ssl/certs/server-fullchain.crt`
   - The HTTPS virtual host was configured for:
     - `web-b.siteb.lab`
     - TCP port `443`

7. **nginx configuration validated**
   - The nginx configuration was checked with:

     `nginx -t`

   - The configuration test completed successfully.
   - Evidence:
     - `Week2-Nginx-HTTPS-Success.png`

8. **HTTPS service listening on port 443**
   - The web server was started/reloaded after successful validation.
   - Port `443` was confirmed as listening on `web-B`.
   - Evidence:
     - `Week2-PKI-Web-B-HTTPS-Port-443-Listening.png`

9. **Network connectivity to web-B**
   - Client-side network connectivity to `web-B` was also tested successfully.
   - Evidence:
     - `Client-B-to-Web-B-Ping-Success.png`

## Result

Site B now has a private two-tier PKI structure:

`Root CA -> Intermediate CA -> web-B Server Certificate`

The Site B web server is configured with the signed certificate and nginx is listening for HTTPS traffic on port `443`.

The server certificate chain was successfully verified during the certificate-issuance process, and the nginx configuration was validated successfully.

## Current Evidence Status

Completed and evidenced:

- Root CA creation
- Intermediate CA creation and verification
- web-B CSR creation
- web-B certificate signing
- Certificate chain verification
- Certificate/key files installed on web-B
- nginx HTTPS configuration
- nginx configuration validation
- HTTPS port `443` listening
- Client-B to web-B network connectivity

A separate final client-side trusted HTTPS verification using the Root CA as the trust anchor should only be claimed if that test is completed and evidenced.

## Security Notes

Private CA keys and server private keys must not be committed to the GitHub repository.

Only screenshots, public certificates, sanitized configuration documentation, and other non-sensitive evidence should be stored in the repository.

## Assignment Mapping

This Week 2 implementation contributes the Site B Certificate Authority and HTTPS server required in the integrated network topology.

The control provides:

- certificate-based server identity
- encrypted HTTPS communication
- a Root CA / Intermediate CA trust hierarchy
- a DMZ-hosted secured web service
