# Site B - Week 4 Evidence: SSH Hardening

**Site:** B (Anima) | **Client:** `client-B` (`10.2.10.30/24`, LAN)

This document records the Week 4 SSH-hardening work integrated into the Site B A2 topology.

## What was done

1. **Site B client prepared for key-based SSH**
   - `client-B` is connected to the Site B LAN.
   - IP address: `10.2.10.30/24`
   - Site B LAN gateway: `10.2.10.1`

2. **Ed25519 key pair generated**
   - An Ed25519 SSH key pair was generated on `client-B`.
   - Private key:
     ```
     /root/.ssh/id_ed25519
     ```
   - Public key:
     ```
     /root/.ssh/id_ed25519.pub
     ```
   - Correct key permissions were verified.

3. **SSH hardening preparation**
   - The Week 4 activity requires key-based authentication, hardened SSH configuration, brute-force protection, and SSH tunnelling.
   - The Site B client-side key material has been prepared for these tests.

## Current Result

The Site B Week 4 SSH activity currently has the Ed25519 client key generation completed and evidenced.

The remaining SSH server-side work includes:

- installing/configuring an SSH server on the selected Site B Linux host
- installing the client public key into `authorized_keys`
- disabling root/password login as required
- configuring authentication limits
- configuring fail2ban
- testing brute-force blocking
- demonstrating SSH tunnelling

These steps should only be marked complete after they have been successfully tested on Site B.

## Evidence

Current screenshot evidence:

- `Week4-SSH-01-Ed25519-Key-Generated.png`

This screenshot shows the successful generation of the Ed25519 private/public key pair on `client-B`.

Additional screenshots should be added after the remaining Week 4 controls are completed.

## Security Note

SSH private keys must never be committed to the GitHub repository.

Only screenshots, configuration evidence, and public-key-related evidence should be stored in the repository.

## Assignment Mapping

The Week 4 SSH-hardening activity contributes to the Site B defence-in-depth design by strengthening administrative access to LAN services through key-based authentication and SSH security controls.
