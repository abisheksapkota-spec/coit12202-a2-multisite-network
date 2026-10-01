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

## Client_SiteA_to_SiteB.pcap

Captured on the `OPNsense1`-`VPNRouter1` link. Filter: `icmp`.

Site A to Site B connectivity over the Tailscale-carried transit link, sourced from `10.1.10.30` (CLIENT) and pinging Site B's VPN router at `10.2.9.2`:

| # | Time | Direction | Type |
|---|---|---|---|
| 5 | 0.0s | `10.1.9.1` to `10.2.9.2` | Echo request (seq 1) |
| 6 | 0.026s | `10.2.9.2` to `10.1.9.1` | Echo reply (seq 1) |
| ... | ... | ... | 5 request/reply pairs total, 0% loss |

**Note on source address:** the ping was run from CLIENT (`10.1.10.30`), but OPNsense1's automatic outbound NAT rewrites the source to its own WAN address (`10.1.9.1`) before it reaches this link. This capture shows firewall-to-firewall (Site A edge to Site B's VPN router) connectivity, confirmed end-to-end; a separate console log shows the same ping run directly from CLIENT with 5/5 replies received.
