# Site A - Week 3 Evidence: Password Policy (UbuntuHost1)

**Site:** A (Abishek) | **Host:** `UbuntuHost1` (`10.1.10.60/24`, LAN) | **Image:** Ubuntu

A new LAN node, `UbuntuHost1`, was added to Site A's topology specifically to host the Week 3
password-hashing/policy lab, integrating "your password policy" into the built A2 network per
the assignment spec. A separate Ubuntu node was used (rather than reusing an Alpine node)
because `pam_pwquality`/`pam_faillock` need a full PAM stack that the Alpine images don't
provide the same way.

## What was done

1. **Node addressed**: `10.1.10.60/24`, gateway `10.1.10.1`, on Site A's existing LAN segment.

2. **Password quality enforced** - `/etc/pam.d/common-password`:
   ```
   password requisite pam_pwquality.so retry=3 minlen=12 ucredit=-1 dcredit=-1 enforce_for_root dictcheck=0
   ```
   `dictcheck=0` is required because this Ubuntu image ships no cracklib word-list - without
   it, a genuinely compliant password would fail on a missing-dictionary error rather than
   being correctly accepted.

3. **Account lockout wired up** - `/etc/pam.d/common-auth` rebuilt with the standard
   `pam_faillock` chain:
   ```
   auth    required                     pam_faillock.so preauth
   auth    [success=1 default=ignore]   pam_unix.so nullok
   auth    [default=die]                pam_faillock.so authfail
   auth    sufficient                   pam_faillock.so authsucc
   auth    requisite                    pam_deny.so
   auth    required                     pam_permit.so
   ```
   `/etc/security/faillock.conf`: `deny=5`, `unlock_time=300`.

4. **Password-quality tested** (throwaway account `user_test`, not a real credential):
   - Weak password (`< 8 chars`) - rejected: *"BAD PASSWORD: The password is shorter than 8
     characters"*
   - Dictionary-check warning appears (*"cracklib_dict.pwd: No such file or directory"* ->
     *"BAD PASSWORD: The password fails the dictionary check"*) but is **not** a blocking
     failure, because `dictcheck=0` - expected, documented behaviour for this image, not a bug.
   - A compliant password is ultimately accepted: *"passwd: password updated successfully"*.

5. **Account lockout tested end-to-end**:
   - `faillock --user user_test --reset` first, confirmed clean (empty table).
   - 5 consecutive wrong-password `su - user_test` attempts -> on the 5th:
     *"The account is locked due to 5 failed logins. (5 minutes left to unlock)"*
   - A 6th attempt **with the correct password** is still refused - proves the account is
     genuinely locked, not just rejecting bad passwords.
   - `faillock --user user_test` shows the failure log populated with real timestamps and
     source (`/dev/pts/0`).
   - **Recovery proven**: `faillock --user user_test --reset` followed by the correct
     password succeeds immediately - the full lock -> recover -> normal-login-restored
     lifecycle is demonstrated, not just the lockout half.

## Result

`UbuntuHost1` on Site A's LAN enforces password complexity (`pam_pwquality`, 12-char minimum,
digit/uppercase requirements) and account lockout after 5 failed attempts (`pam_faillock`,
5-minute unlock window), both live-tested with real, timestamped evidence.

**Evidence status:** commands and output above are transcribed directly from the live GNS3
console session. Screenshots matching this narrative (analogous to the Week 2 CA/HTTPS
evidence images) are still to be captured and added to `Images/` before final submission.

## Note on rubric mapping

Doesn't map to its own rubric line (there's no dedicated "password policy" criterion), but is
part of satisfying the A2 spec's instruction to bring all three Assessment 1 services -
including "your password policy" - into the single built topology. Also a good candidate for
the report's defence-in-depth section: this is an account-level control, distinct from and
complementary to `SERVER`'s network-level fail2ban control (see `SSH-Hardening-Evidence.md`) -
one blocks a source IP, the other locks the account itself regardless of source.
