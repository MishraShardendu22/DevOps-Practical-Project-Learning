# Git Signing, SSH, GPG, Verified Badge — Final Notes

---

## 1) Two Separate Systems

### A) SSH

* Purpose: Authenticate + encrypt connection
* Used when pushing/pulling
* Controls **access**
* Does NOT modify commits

### B) Commit Signing (GPG / SSH signing)

* Purpose: Prove commit authorship & integrity
* Embeds cryptographic signature **inside commit**
* Controls **identity of author**

---

## 2) Why SSH alone doesn’t give Verified badge

SSH answers:

> Are you allowed to push?

Verified badge answers:

> Was this commit cryptographically signed?

So: **SSH push ≠ signed commit**

---

## 3) What produces Verified badge on GitHub

Only commits that contain a cryptographic signature.

Two signing methods:

* GPG (OpenPGP)
* SSH signing mode

---

## 4) SSH Push vs SSH Signing

| Feature             | Purpose                 |
| ------------------- | ----------------------- |
| SSH key for push    | Authenticate connection |
| SSH key for signing | Sign commit             |

Same key file can exist, but **Git must be told** to use SSH for signing:

```md
git config --global gpg.format ssh
```

Without this, SSH key is only for transport.

---

## 5) GPG Signing (Most common)

Flow:

```md
gpg --gen-key
git config --global user.signingkey <KEYID>
git config --global commit.gpgsign true
```

Result: every commit is signed.

---

## 6) Multiple GitHub Accounts

### SSH Keys

* Usually one SSH key per account
* Managed via ~/.ssh/config

### GPG Keys

Two valid models:

**Option A (simple)** -

* One GPG key
* Upload same public key to all accounts
* Verified everywhere

**Option B** -

* One GPG key per account
* More isolation

Most people use Option A.

---

## 7) Verification Pipeline

1. You commit
2. Git checks `commit.gpgsign`
3. If true → signs commit
4. You push (SSH only transports)
5. GitHub checks signature vs uploaded public keys
6. Match → Verified badge

---

## 8) Meaning of Important Configs

### Auto signing

```md
git config --global --get commit.gpgsign
```

* true → all commits signed
* empty → none signed

---

### Signing backend

```md
git config --global --get gpg.format
```

* openpgp → GPG
* ssh → SSH signing
* empty → default = GPG

---

### Signing key

```md
git config --global --get user.signingkey
```

---

### Local verification

```md
git log --show-signature -1
```

Look for:

```md
gpg: Good signature
```

or

```md
Good "git" signature with SSH key
```

---

## 9) Minimal Working Setup (Recommended)

```md
gpg --gen-key
git config --global user.signingkey <KEYID>
git config --global commit.gpgsign true
```

Upload public key to GitHub.

Done.

---

## 10) Core Mental Model

* SSH key → permission to push
* GPG/SSH signing key → identity of author
* Verified badge → signed commit exists

---
