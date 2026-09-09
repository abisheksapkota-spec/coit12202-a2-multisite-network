# Captures

## Auth-Kerberos-12312491-client.pcap

Captured on the `client`-`Switch1` link. Filter: `kerberos`.

**6 Kerberos packets, not 4** - `kinit` was accidentally run twice in a row, producing two full AS-REQ/AS-REP exchanges instead of one:

| # | Time | Direction | Type |
|---|---|---|---|
| 3 | 0.0003s | client to kdc | AS-REQ |
| 4 | 0.005s | kdc to client | AS-REP |
| 7 | 21.98s | client to kdc | AS-REQ |
| 8 | 21.98s | kdc to client | AS-REP |
| 39 | 101.93s | client to kdc | TGS-REQ |
| 40 | 101.93s | kdc to client | TGS-REP |

**Negative proof is also in this same file** - around packet 1807, a second SSH connection appears: full TCP handshake and SSH key exchange, but zero Kerberos packets before it, closing in well under a second (versus about 55 seconds for the real session). That's the `kdestroy` and retry attempt failing before it could even request a ticket.

## Still needed

- [ ] Site-to-site VPN before/after pair, once the tunnel exists
