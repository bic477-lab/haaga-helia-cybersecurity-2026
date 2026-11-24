# H5 – Alice and Bob

**Course:** Cyber Security (ICB705AS3YE-3002), Haaga-Helia University of Applied Sciences  
**Student:** Sherif Hassan  
**Date:** 24 November 2026

## Laboratory environment

I used Debian GNU/Linux 13.5 (Trixie) on Windows 11 with WSL2, GnuPG 2.4.7, Libgcrypt 1.11.0, Pass 1.7.4 and Python 3.13.5. Alice and Bob were simulated using separate GPG home directories under the same Linux user.

```bash
sudo apt update
sudo apt install -y gnupg pass python3 pinentry-curses
mkdir -p ~/labs/h5-alice-and-bob
cd ~/labs/h5-alice-and-bob
mkdir -p alice bob exchange password-manager evidence
chmod 700 alice bob password-manager
export GPG_TTY=$(tty)
```

All relative paths below are from `~/labs/h5-alice-and-bob`. Private keys, revocation certificates and password values are excluded from this public report.

## x) Reading summaries

### Schneier (2015) – Applied Cryptography

**Chapter 1, Section 1.1: Terminology**

- Encryption transforms plaintext into ciphertext; decryption recovers the plaintext. Cryptography supports confidentiality, authentication, integrity and nonrepudiation.
- Modern cryptosystems rely on protecting keys rather than hiding algorithms.
- Symmetric cryptography uses a shared secret key. Block and stream ciphers process data differently; secure key distribution is a major challenge.
- Public-key cryptography uses mathematically related public and private keys. The public key can be distributed, while the private key must remain secret.

**Chapter 2, Section 2.5: Communications Using Public-Key Cryptography**

- Alice encrypts for Bob using his public key; Bob decrypts using the corresponding private key. Authenticating the public key remains essential.
- Hybrid cryptography uses public-key operations to protect a randomly generated session key and symmetric encryption to protect the actual message efficiently.
- Merkle's puzzles illustrate an early key-establishment approach in which an eavesdropper must perform substantially more work than the communicating parties.

**Chapter 2, Section 2.6: Digital Signatures**

- Symmetric signature protocols can require a trusted arbitrator; public-key signatures allow direct verification without an arbitrator for each transaction.
- The sender signs using a private key; the recipient verifies using the corresponding public key. Hash functions make signing large documents efficient.
- Timestamps and transaction records help address replay; multiple parties can sign a document. Nonrepudiation depends on authentic key identities and protection of private keys.

### Karvinen (2023) – PGP: Send Encrypted and Signed Message – gpg

- GnuPG implements OpenPGP encryption and digital signatures. Separate GPG home directories can simulate two users.
- Users generate key pairs, exchange only public keys, and independently verify fingerprints before certifying identities.
- Alice signs with her private key and encrypts for Bob using his public key. Bob decrypts with his private key and verifies with Alice's public key.
- ASCII armor makes binary OpenPGP data printable; it is encoding, not additional encryption.

## a) Pubkey today

On 24 November 2026, I used Gmail and LinkedIn over HTTPS for personal purposes outside this homework. HTTPS uses TLS to secure communication between the browser and web server.

![Task A: HTTPS and public-key cryptography](images/h5-task-a.png)

*Figure 1. Conceptual overview of certificate authentication, key agreement and encrypted HTTPS communication. The boxes illustrate roles, not the precise TLS handshake message order.*

In a typical certificate-authenticated TLS 1.3 handshake with ECDHE:

| Party | Key and operation |
|---|---|
| Certificate authority | CA private key signs the server certificate. |
| Browser | Trusted CA public keys validate the certificate chain and the browser checks the server identity. |
| Browser and server | Exchange ephemeral public values; each combines its own ephemeral private key with the peer's public value to derive a shared secret. |
| Server | Private signing key signs the handshake transcript. |
| Browser | Server public key verifies the signature. |
| Both | Derive symmetric traffic keys to encrypt and authenticate application data. |

In TLS 1.3, the ephemeral key exchange precedes certificate validation in a typical full handshake. My specific session versions and cipher suites were not inspected; this is a standards-based explanation, not a packet-capture result.

As a telecommunications engineer, I recognize similar principles in IPsec VPNs using IKEv2, which combines key agreement, authentication and symmetric encryption for secure transport networks.

## b) Encrypt and decrypt a message

![Task B: Alice encrypts and Bob decrypts](images/h5-task-b.png)

*Figure 2. Hybrid OpenPGP encryption and the positive and negative decryption tests. The negative test was executed using Alice's keyring; both files were compared locally in the lab.*

### 1. Generate and exchange Bob's keys

```bash
gpg --homedir "$PWD/bob" --full-generate-key
gpg --homedir "$PWD/bob" --list-secret-keys
gpg --homedir "$PWD/bob" --armor \
    --output exchange/bob-public.asc --export bob@example.invalid
gpg --homedir "$PWD/alice" --import exchange/bob-public.asc
gpg --homedir "$PWD/bob" --fingerprint
gpg --homedir "$PWD/alice" --fingerprint
gpg --homedir "$PWD/alice" --list-secret-keys
```

I selected RSA and RSA, 3072 bits, one-year validity, and a passphrase. Bob's primary key had signing and certification capabilities `[SC]`, and his encryption subkey had capability `[E]`. Both keyrings displayed the same primary fingerprint (`E7C587BAAF779FF90B46BC9B34FB47698ECB5122`). Alice had no private keys at this stage.

### 2. Encrypt the message

I created a 315-byte fictional message about secure telecommunications and encrypted it:

```bash
cat > alice/message.txt <<'EOF'
Hello Bob,

This is a confidential test message from Alice.

The purpose of this experiment is to demonstrate
secure communication using public-key cryptography.

A similar cryptographic principle is used in
telecommunication networks to establish secure
communication over untrusted networks.

Best regards,
Alice
EOF

gpg --homedir "$PWD/alice" --armor \
    --output exchange/message-encrypted.asc --encrypt \
    --recipient E7C587BAAF779FF90B46BC9B34FB47698ECB5122 \
    alice/message.txt
ls -lh alice/message.txt exchange/message-encrypted.asc
```

I accepted GPG's key-validity warning only after comparing fingerprints. The encrypted file was 967 bytes. GPG selected Bob's encryption subkey `D71D2E898038563B`.

### 3. Test decryption and compare files

```bash
# Negative test: Alice has Bob's public key only
gpg --homedir "$PWD/alice" --decrypt exchange/message-encrypted.asc
echo $?

# Positive test: Bob has the corresponding private key
gpg --homedir "$PWD/bob" --verbose \
    --output bob/decrypted.txt --decrypt exchange/message-encrypted.asc
echo $?
cat bob/decrypted.txt
sha256sum alice/message.txt bob/decrypted.txt
cmp alice/message.txt bob/decrypted.txt \
    && echo "SUCCESS: Both files are identical"
```

**Observed results:**

| Test | Result |
|---|---|
| Alice decrypts | `No secret key` – exit code 2 |
| Bob decrypts | `AES256.CFB encrypted data` – exit code 0 |
| SHA-256 comparison | Identical hashes |
| Byte comparison | `SUCCESS: Both files are identical` |

Both files had the SHA-256 digest:

```text
2bf5573c6ca03dfa78f2ca633a5d6d67bd786f19282812566aa58b8f18dc8465
```

**Key usage and security evaluation:** GnuPG used Bob's RSA public encryption subkey to protect a random session key, and AES-256 CFB to encrypt the message. Bob used his private encryption subkey to recover the session key and decrypt the data. Alice could not decrypt the ciphertext with her keyring. GnuPG is an established open-source OpenPGP implementation; RSA-3072 and AES-256 provide strong classical protection when used correctly. CFB alone does not authenticate data; OpenPGP supplies additional integrity protection. Secure endpoints, authentic public keys, protected private keys and revocation procedures remain essential. Long-term RSA key compromise can expose previously captured messages. Encryption alone does not authenticate the sender, and this experiment is not a security audit of the tool.

## c) Password manager

I demonstrated Pass, the standard Unix password manager. It stores each credential in a separate GPG-encrypted file.

![Task C: Pass and GPG password manager](images/h5-task-c.png)

*Figure 3. Password generation, encrypted storage and retrieval with Bob's key, and rejection of decryption using Alice's keyring. Paths are relative to the lab directory; the credentials are fictional.*

### 1. Initialize, generate and inspect

```bash
export GNUPGHOME="$PWD/bob"
export PASSWORD_STORE_DIR="$PWD/password-manager"
export GPG_TTY=$(tty)
pass init E7C587BAAF779FF90B46BC9B34FB47698ECB5122
pass generate email/gmail-demo 24
pass generate social/linkedin-demo 24
pass generate network/vpn-demo 32
pass
find "$PASSWORD_STORE_DIR" -type f
od -Ax -tx1 -N 128 "$PASSWORD_STORE_DIR/email/gmail-demo.gpg"
```

The `file` and `xxd` tools were unavailable; `od` successfully displayed the encrypted binary data. Its OpenPGP packet header contained Bob's encryption subkey ID. All four credential files eventually had permissions `600`, and `.gpg-id` contained Bob's primary fingerprint.

### 2. Retrieve and insert credentials; test unauthorized decryption

```bash
pass show email/gmail-demo
(
set -o pipefail
for entry in email/gmail-demo social/linkedin-demo network/vpn-demo
do
    echo "$entry"
    pass show "$entry" | python3 -c \
    'import sys; print("Password length:", len(sys.stdin.readline().rstrip("\n")))'
done
)
pass insert lab/manual-demo
pass show lab/manual-demo > /dev/null
echo $?
GNUPGHOME="$PWD/alice" pass show email/gmail-demo > /dev/null
echo $?
echo "$GNUPGHOME"
```

**Observed results:**

| Entry | Recovered length | Encrypted size |
|---|---:|---:|
| Gmail demo | 24 | 475 bytes |
| LinkedIn demo | 24 | 475 bytes |
| VPN demo | 32 | 483 bytes |
| Manual demo | Not measured | 461 bytes |

The recovered Gmail password matched the generated password. Manual retrieval returned exit code 0. The negative test returned `No secret key` and exit code 2. Bob's GPG home remained active after the temporary environment override. Password values are intentionally omitted.

### 3. Attacks exploiting weak password management

| Attack | How a password manager reduces risk |
|---|---|
| Credential stuffing | Unique credentials limit reuse of stolen logins across services. |
| Brute force / offline cracking | Long random passwords make guessing harder, even against leaked hashes. |
| Dictionary attacks | Random generation avoids common words and predictable patterns. |
| Password spraying | Unique, unpredictable passwords reduce exposure to common guesses. |
| Insecure storage | GPG encryption protects stored contents from readers without decryption access. |

Pass helps protect credentials but does not eliminate phishing, malware or endpoint compromise. Filenames can expose account metadata. Protect the GPG private key and passphrase, back up securely, and use MFA or phishing-resistant passkeys where available.

## d) Encrypt and digitally sign a message (voluntary)

![Task D: Alice signs and encrypts, Bob decrypts and verifies](images/h5-task-d.png)

*Figure 4. Mutual key certification, sign-and-encrypt workflow, and the detached-signature tampering test.*

### 1. Generate Alice's keys and certify both identities

```bash
unset GNUPGHOME PASSWORD_STORE_DIR
export GPG_TTY=$(tty)
gpg --homedir "$PWD/alice" --full-generate-key
gpg --homedir "$PWD/alice" --armor \
    --output exchange/alice-public.asc \
    --export FB0D14036265DC421CBA0C846EF82821E536B700
gpg --homedir "$PWD/bob" --import exchange/alice-public.asc
gpg --homedir "$PWD/alice" --fingerprint alice@example.invalid
gpg --homedir "$PWD/bob" --fingerprint alice@example.invalid

gpg --homedir "$PWD/alice" --lsign-key E7C587BAAF779FF90B46BC9B34FB47698ECB5122
gpg --homedir "$PWD/bob" --lsign-key FB0D14036265DC421CBA0C846EF82821E536B700
gpg --homedir "$PWD/alice" --list-keys
gpg --homedir "$PWD/bob" --list-keys
```

Alice also used passphrase-protected RSA-3072 keys valid for one year. After exchanging public keys and comparing fingerprints, I locally certified both identities using non-exportable signatures (`--lsign-key`). The imported identities changed from `[unknown]` to `[full]`; each party's own identity remained `[ultimate]`. The initial wrong-directory and missing-import errors were resolved by returning to the lab directory and importing Alice's public key.

### 2. Sign, encrypt, decrypt and verify

I created a new 360-byte message and signed and encrypted it:

```bash
cat > alice/signed-message.txt <<'EOF'
Hello Bob,

This is a confidential and digitally signed message from Alice.

This experiment demonstrates three essential
cryptographic security objectives:

1. Confidentiality
2. Authentication
3. Integrity

These security principles are also important in
modern telecommunications, secure VPN connections,
and 5G network infrastructure.

Best regards,
Alice
EOF

gpg --homedir "$PWD/alice" --armor \
    --output exchange/message-signed-encrypted.asc \
    --local-user FB0D14036265DC421CBA0C846EF82821E536B700 \
    --recipient E7C587BAAF779FF90B46BC9B34FB47698ECB5122 \
    --sign --encrypt alice/signed-message.txt

gpg --homedir "$PWD/bob" --verbose \
    --output bob/signed-decrypted.txt \
    --decrypt exchange/message-signed-encrypted.asc
echo $?
sha256sum alice/signed-message.txt bob/signed-decrypted.txt
cmp alice/signed-message.txt bob/signed-decrypted.txt \
    && echo "SUCCESS: Both files are identical"
```

**Observed output (selected lines):**

```text
gpg: AES256.CFB encrypted data
gpg: using RSA key FB0D14036265DC421CBA0C846EF82821E536B700
gpg: Good signature from "Alice H5 Lab (Cyber Security H5) <alice@example.invalid>" [full]
gpg: binary signature, digest algorithm SHA512, key algorithm rsa3072
0
SUCCESS: Both files are identical
```

The encrypted/signed file was approximately 1.7 KB. Both plaintext files had the same SHA-256 digest:

```text
aa569e2509788b1fc4d0deb88e190623e49818fe2d2c3ee2c13fec815e733fc5
```

| Operation | Key |
|---|---|
| Alice signs | Alice's private RSA signing key (SHA-512 digest) |
| Alice encrypts the session key | Bob's public RSA encryption subkey |
| Bob recovers the session key | Bob's private RSA encryption subkey |
| Bob verifies the signature | Alice's public RSA signing key |

### 3. Additional test: detect message tampering

```bash
gpg --homedir "$PWD/alice" --armor \
    --local-user FB0D14036265DC421CBA0C846EF82821E536B700 \
    --output exchange/message-signature.asc \
    --detach-sign alice/signed-message.txt

gpg --homedir "$PWD/bob" --verify exchange/message-signature.asc \
    bob/signed-decrypted.txt
echo $?

cp bob/signed-decrypted.txt bob/tampered-message.txt
echo "This message has been modified." >> bob/tampered-message.txt
gpg --homedir "$PWD/bob" --verify exchange/message-signature.asc \
    bob/tampered-message.txt
echo $?
```

| Verification | Observed result |
|---|---|
| Original message | `Good signature` [full] – exit code 0 |
| Modified copy | `BAD signature` [full] – exit code 1 |

The same public key and detached signature were used in both tests. The modified content invalidated the signature even though the signing key's identity remained `[full]`. This demonstrates that key identity validity and message signature validity are separate concepts. Signing and encryption together provide confidentiality, authentication and integrity under appropriate key-management assumptions.

## Conclusion

The laboratory demonstrated public-key and hybrid encryption, password management, digital signatures and detection of modified signed data. The positive and negative tests confirmed expected GnuPG behavior. Alice and Bob were separate keyrings under the same Linux account, not independently secured systems. All diagrams are explanatory illustrations based on the workflow, not screenshots of the terminal. No private key files, revocation certificates or password values are included.

## References

- Schneier, B. (2015). *Applied Cryptography: Protocols, Algorithms and Source Code in C*, 20th anniversary edition. Wiley. Chapter 1 §1.1 (through algorithms); Chapter 2 §§2.5–2.6. [O'Reilly](https://learning.oreilly.com/library/view/applied-cryptography-protocols/9781119096726/).
- Karvinen, T. (2023). [PGP – Send Encrypted and Signed Message – gpg](https://terokarvinen.com/2023/pgp-encrypt-sign-verify/).
- Rescorla, E. (2018). [TLS 1.3, RFC 8446](https://www.rfc-editor.org/rfc/rfc8446.html), §§2, 4, 7.
- Kaufman, C. et al. (2014). [IKEv2, RFC 7296](https://www.rfc-editor.org/rfc/rfc7296.html).
- [GnuPG 2.4 manual](https://gnupg.org/documentation/manuals/gnupg24/gpg.1.html).
- [Pass – The Standard Unix Password Manager](https://www.passwordstore.org/).
- OWASP. [Credential Stuffing Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Credential_Stuffing_Prevention_Cheat_Sheet.html).
