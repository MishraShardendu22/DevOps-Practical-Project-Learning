# GPG / SSH / GitHub Verified — Consolidated Notes

---

## GPG (GNU Privacy Guard)

* Open-source implementation of OpenPGP standard
* Uses **public-key cryptography**
* Key pair:

  * Public key → share
  * Private key → secret

**Used for:**

* Encrypting files / messages
* Decrypting data
* Creating digital signatures
* Verifying signatures

**Examples** -

```md
gpg --gen-key
gpg -e file.txt
gpg -d file.txt.gpg
gpg --sign file
gpg --verify file.gpg
```

---

## SSH Keys

* Also use public/private key cryptography
* Purpose: authenticate user + create encrypted remote session

**Used for:**

* Logging into servers
* Git push/pull authentication
* Secure tunnels

**Not interchangeable with GPG keys** -

---

## Concept Relationship

* GPG and SSH use same **cryptographic principle**
* Different application domains:

| Tool | Purpose                                    |
| ---- | ------------------------------------------ |
| GPG  | Data encryption & signing                  |
| SSH  | Identity authentication & secure transport |

---

## Git Commit Signing

Using GPG:

```md
git commit -S -m "message"
```

* Commit contains cryptographic signature
* Proves:

  * Who made commit
  * Commit not altered

---

## GitHub Verified Badge

On GitHub:

* Appears when commit has valid cryptographic signature
* Usually GPG-signed
* Can also be SSH-signed

**Verified badge = cryptographic signature present** -

---

## DCO (Developer Certificate of Origin)

```md
Signed-off-by: Name <email>
```

* Plain text line in commit message
* Legal declaration
* **No cryptography**
* **No keys**

---

## Comparison

| Feature            | Uses Keys | Cryptographic | Purpose              |
| ------------------ | --------- | ------------- | -------------------- |
| GPG Commit Signing | Yes       | Yes           | Identity + Integrity |
| SSH Signing        | Yes       | Yes           | Identity + Integrity |
| DCO                | No        | No            | Legal attestation    |

---

### Final Mental Model

* GPG → encrypt & sign data
* SSH → authenticate & secure connections
* Verified badge → cryptographic signature
* DCO → legal checkbox only

---
