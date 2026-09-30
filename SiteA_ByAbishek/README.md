# Site A - Week 2 Evidence: CA + HTTPS Server

**Site:** A (Abishek) | **Realm/domain:** `SITEA.LOCAL` | **Service host:** `www.sitea.local` (`10.1.20.20`)

This folder documents standing up a private CA (root + intermediate) and using it to
issue a server certificate for Site A's web server, then proving the full chain of
trust end-to-end from a separate client host.

## What was done

1. **Intermediate CA created and signed** - a root CA and an intermediate CA were
   built with `openssl genpkey` / `openssl req` / `openssl x509 -req`, with the
   intermediate signed by the root. `root-ca.crt` and `intermediate.crt` both exist
   on the CA node afterwards (`10.1.10.50`, LAN).
   ![Intermediate CA created and signed](Images/ca-01-intermediate-created-and-signed.png)

2. **SSH login to webserver** (`10.1.20.20`, Alpine Linux) to do the server-side
   certificate work.
   ![SSH login to webserver](Images/ca-02-ssh-login-to-webserver.png)

3. **Server certificate signed by the CA** - a CSR for `www.sitea.local` was signed
   by the intermediate CA (`openssl x509 -req -CA intermediate.crt -CAkey
   intermediate.key`), producing `server.crt` with `CN=www.sitea.local` and
   `SAN=DNS:www.sitea.local`. The cert and the CA chain (`ca-chain.crt`, i.e.
   intermediate + root concatenated) were copied to webserver over `scp`.
   ![Server certificate signed by the CA](Images/ca-03-server-certificate-signed-by-ca.png)

4. **Web server key/cert setup and nginx validation** - a fresh 2048-bit RSA key
   and matching CSR were generated directly on webserver, signed by the CA, and the
   full chain (`server.crt` + `ca-chain.crt`) written to
   `/etc/ssl/certs/server-fullchain.crt`. `nginx -t` confirms
   *"syntax is ok / test is successful."*
   ![Web server key/cert setup and nginx validation](Images/ca-04-webserver-key-setup-nginx-validation.png)

5. **Local TLS proof on webserver** - `curl -vk https://127.0.0.1/` shows a full
   TLS 1.3 handshake (`TLS_AES_256_GCM_SHA384`), the server certificate presented
   (`CN=www.sitea.local`, issued by `CN=SiteA Intermediate CA`), and
   `HTTP/1.1 200 OK` returning the page body
   `<h1>Site A - Secured Multi-Site Network</h1>`.
   ![Local TLS proof via curl -vk](Images/ca-05-local-tls-proof-curl-vk.png)

6. **Bug hit and fixed** - `nginx -t`/`nginx` were run before `server.key` existed
   at `/etc/ssl/private/server.key`, producing
   `nginx: [emerg] cannot load certificate key ... BIO_new_file failed, no such
   file`. A `curl -vk` at that point also failed with connection refused (nginx
   wasn't running). Fixed by generating the key/CSR/cert in the correct order
   before starting nginx.
   ![nginx bug and fix](Images/ca-06-bug-nginx-key-missing-then-fixed.png)

7. **Client-side verification (the main evidence)** - from a separate CLIENT host:
   `/etc/hosts` updated to resolve `www.sitea.local` to `10.1.20.20`;
   `root-ca.crt` and `intermediate.crt` pulled from the CA node (`10.1.10.50`) and
   `server.crt` from webserver, all via `scp`;
   `openssl verify -CAfile root-ca.crt -untrusted intermediate.crt server.crt`
   returns **`OK`**; `curl --cacert root-ca.crt https://www.sitea.local/` returns
   the page body, confirming the client trusts the chain without `-k` and without
   any CA installed in its system trust store. (First attempt failed transiently
   with "Could not connect to server" - an immediate retry succeeded; this looks
   like a timing issue rather than a chain-of-trust problem, since the retry used
   an identical command.)
   ![Client-side chain verification success](Images/ca-07-client-side-chain-verification-success.png)

## Result

A private two-tier CA (root -> intermediate) issues a certificate for
`www.sitea.local`, nginx serves it correctly, and a separate client verifies the
full chain and connects over HTTPS without disabling certificate checking. All
screenshots above were checked frame-by-frame against this description before
being committed.

## Note on rubric mapping

Maps to **Criterion 2 (Firewalls & zones)** - the A2 spec asks for "your certificate
authority and HTTPS server" to be brought into the built topology, and the rubric
requires the DMZ zone to hold this as an actual running service. See
`RUBRIC-CHECKLIST.md` Section 2. Also a good candidate for the report's defence-in-
depth section, alongside Site A's other two integrated Assessment 1 controls (see
`SSH-Hardening-Evidence.md`, `Password-Policy-Evidence.md`).
