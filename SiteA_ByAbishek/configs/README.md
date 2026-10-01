# Configs

Exported and written configuration for Site A's devices.

| File | Device / Scope | Contents |
|---|---|---|
| `kerberos-summary.md` | KDC, SERVER, CLIENT (`SITEA.LOCAL` realm) | Full command-by-command build record: KDC database initialisation, principal creation (`abishek`, `host/server.sitea.local`, `host/client.sitea.local`, `host/kdc.sitea.local`), keytab export and transfer, the two bugs hit and fixed (locked Unix account, hostname case-mismatch), and the positive/negative GSSAPI SSH login proof, with supporting screenshots. |

## Kerberos build detail (`kerberos-summary.md`)

**Realm:** `SITEA.LOCAL` · **User principal:** `abishek`

**Commands used:**
```
kdb5_util create -s -P abishekkerberos
touch /var/lib/krb5kdc/kadm5.acl
krb5kdc
kadmind

kadmin.local
addprinc -pw abishekkerberos abishek
addprinc -randkey host/server.sitea.local
addprinc -randkey host/client.sitea.local
addprinc -randkey host/kdc.sitea.local
ktadd -k /tmp/server.keytab host/server.sitea.local

# on kdc
python3 -m http.server 8080 &
# on server
curl -s -o /etc/krb5.keytab http://kdc.sitea.local:8080/server.keytab
```

**Two bugs hit and fixed:**

| Bug | Symptom | Root cause | Fix |
|---|---|---|---|
| Locked account | GSSAPI SSH failed with a generic `Permission denied` | An earlier failed `passwd` attempt had locked the `abishek` Unix account - confirmed via `sshd.krb5 -ddd` debug output showing `account is locked` | `passwd -u abishek` |
| Hostname mismatch | GSSAPI SSH failed even with a valid ticket and keytab | `server`'s live kernel hostname was `SERVER`, not `server.sitea.local` - Kerberos hostname matching is case-sensitive | `hostname server.sitea.local`, then restart `sshd.krb5` |

**Proof:**
```
kinit abishek
ssh -o GSSAPIAuthentication=yes -o PasswordAuthentication=no -o PreferredAuthentications=gssapi-with-mic abishek@server.sitea.local
```
Logged in with no password prompt. After `kdestroy`, the identical command returned `Permission denied` - confirming the login mechanism was the Kerberos ticket itself, not something else. Screenshots of both outcomes are included in `kerberos-summary.md`.
