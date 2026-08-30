# Site A — Kerberos Authentication (Week 5)

Owner: Abishek · Realm: `SITEA.LOCAL` · Hosts: `kdc` (10.1.10.10) · `server` (10.1.10.20) · `client` (10.1.10.30)

Built from the original Week 5 individual lab (realm `EXAMPLE.COM`, `10.10.1.0/24`), re-realmed and re-addressed for the multi-site build. All checkpoints below are evidenced by screenshots in `../Images/` and the packet capture in `../captures/`.

## Task 1 — Addressing and realm

`/etc/krb5.conf` renamed from `EXAMPLE.COM` to `SITEA.LOCAL` on all three hosts; `/etc/hosts` updated with `sitea.local` entries; addressing moved from the standalone `10.10.1.0/24` into Site A's real LAN (`10.1.10.0/24`), behind OPNsense.

**Evidence:** `kerberos-01-krb5conf-realm.png`

## Task 2 — KDC database and daemons

```
kdb5_util create -s -P abishekkerberos
touch /var/lib/krb5kdc/kadm5.acl
krb5kdc
kadmind
```

**Checkpoint:** `pgrep krb5kdc` and `pgrep kadmind` both returned a PID.
**Evidence:** `kerberos-02-kdc-database-init.png`

## Task 3 — Principals and server keytab

```
kadmin.local
addprinc -pw abishekkerberos student
addprinc -randkey host/server.sitea.local
addprinc -randkey host/client.sitea.local
ktadd -k /tmp/server.keytab host/server.sitea.local
```

**Checkpoint:** `listprincs` shows `student@SITEA.LOCAL`, `host/server.sitea.local@SITEA.LOCAL`, `host/client.sitea.local@SITEA.LOCAL` plus Kerberos defaults. Keytab written with both `aes256` and `aes128` entries.
**Evidence:** `kerberos-03-principals-keytab-export.png`

## Task 4 — Keytab installed on server

Transferred via HTTP (no credentials needed):
```
# on kdc
cd /tmp && python3 -m http.server 8080 &
# on server
curl -s -o /etc/krb5.keytab http://kdc.sitea.local:8080/server.keytab
```

**Checkpoint:** `klist -k /etc/krb5.keytab` on `server` lists `host/server.sitea.local@SITEA.LOCAL` twice (KVNO 2).
**Evidence:** `kerberos-04-server-keytab-installed.png`

## Task 5 — GSSAPI and the Kerberos SSH daemon

`/etc/ssh/sshd_config` on `server`: uncommented `GSSAPIAuthentication yes` and `GSSAPICleanupCredentials yes`. Ran `ssh-keygen -A` (no default host keys on this image), then started `/usr/sbin/sshd.krb5`.

**Checkpoint:** `pgrep -f sshd.krb5` returns a PID.
**Evidence:** `kerberos-05-gssapi-sshd-running.png`

## Task 6 — Ticket on client

```
kinit student
klist
```

**Checkpoint:** `Default principal: student@SITEA.LOCAL`, valid `krbtgt/SITEA.LOCAL@SITEA.LOCAL` ticket.
**Evidence:** `kerberos-06-client-kinit-ticket.png`

## Task 7 — Kerberos-only login, proven both ways

```
ssh -o GSSAPIAuthentication=yes -o PasswordAuthentication=no \
    -o PreferredAuthentications=gssapi-with-mic student@server.sitea.local
```

Logged in with **no password prompt** — proof positive.

```
exit
kdestroy
klist
```

Ticket cache confirmed empty (`No credentials cache found`).

```
ssh -o GSSAPIAuthentication=yes -o PasswordAuthentication=no \
    -o PreferredAuthentications=gssapi-with-mic student@server.sitea.local
```

Same exact command, now **refused** — `Permission denied (publickey,gssapi-with-mic,password,keyboard-interactive)`. This is proof negative: the only thing that changed was the ticket.

**Evidence:** `kerberos-07-ssh-login-success.png`, `kerberos-08-kdestroy-tickets-cleared.png`, `kerberos-09-ssh-denied-no-ticket.png`

## Packet capture

Captured on the `client`–`Switch1` link, filter `kerberos`. Exactly 4 packets, matching expectation:

| # | Src | Dst | Message |
|---|---|---|---|
| 1 | 10.1.10.30 (client) | 10.1.10.10 (kdc) | AS-REQ |
| 2 | 10.1.10.10 | 10.1.10.30 | AS-REP |
| 30 | 10.1.10.30 | 10.1.10.10 | TGS-REQ |
| 31 | 10.1.10.10 | 10.1.10.30 | TGS-REP |

AS exchange came from `kinit`; TGS exchange appeared once the SSH connection began authenticating.

**Evidence:** `../captures/Auth-Kerberos-12312491-client.pcap`

## One real bug worth documenting in the report

`sshd.krb5` initially rejected the GSSAPI login with `No key table entry found matching host/server@` — despite the keytab and config all being correct. Root cause: the Docker container's live kernel hostname (`server`) didn't match `/etc/hostname` (`server.sitea.local`), which had been set by editing the file directly rather than through the container's actual boot process. Fixed live with `hostname server.sitea.local`, after which GSSAPI authentication succeeded immediately. Worth a line in the report's troubleshooting/justification section — it's a good example of a subtle but fully diagnosed and resolved fault.
