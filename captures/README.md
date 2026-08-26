# Packet capture evidence

Shared evidence used across the report. Naming convention: `<what>-<before|after>-<siteX-siteY>.pcapng`.

**Required (non-negotiable):**
- `vpn-before-<siteX-siteY>.pcapng` — cross-site traffic captured **before** the tunnel is up, payload readable
- `vpn-after-<siteX-siteY>.pcapng` — the **same** traffic, captured **after** the tunnel is up, opaque (ESP or WireGuard)

Good practice: use the cross-realm Kerberos exchange as the "same traffic" for this pair — it's real application traffic and doubles as evidence for the authentication section.

For every file placed here, add an entry below stating where it was captured, the display filter used, and a one-line summary of what it proves. This mirrors what must also appear in the report.

| File | Captured at | Filter | Proves |
|---|---|---|---|
| *(fill in as you add captures)* | | | |
