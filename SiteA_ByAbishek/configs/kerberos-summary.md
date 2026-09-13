# Site A - Kerberos Build Record

Realm: `SITEA.LOCAL` · User principal: `abishek`

## Commands used

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

## Two bugs hit and fixed

**Locked account:** an earlier failed `passwd` attempt locked the `abishek` Unix account, blocking GSSAPI with a generic `Permission denied`. Found via debug mode (`sshd.krb5 -ddd`): `User abishek not allowed because account is locked`. Fixed with `passwd -u abishek`.

**Hostname mismatch:** `server`'s live hostname was `SERVER`, not `server.sitea.local`. Kerberos matching is case-sensitive. Fixed with `hostname server.sitea.local` + restart `sshd.krb5`.

## Proof

```
kinit abishek
ssh -o GSSAPIAuthentication=yes -o PasswordAuthentication=no -o PreferredAuthentications=gssapi-with-mic abishek@server.sitea.local
```
Logged in, no password. Then `kdestroy` + same command → `Permission denied`. Positive and negative proof both confirmed.



<img width="940" height="315" alt="image" src="https://github.com/user-attachments/assets/1d4fc3ea-0cba-443f-b14b-598b32cb8e26" />


<img width="940" height="310" alt="image" src="https://github.com/user-attachments/assets/39485058-192b-4eaf-bd84-129e413a9036" />


<img width="940" height="586" alt="image" src="https://github.com/user-attachments/assets/bb00eab1-9b50-44d8-805a-a26e8aac1704" />
