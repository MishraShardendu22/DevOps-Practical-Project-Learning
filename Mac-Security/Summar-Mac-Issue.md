# macOS SSH User Creation & Troubleshooting (Linux Mindset Guide)

This document explains how macOS user accounts differ from Linux,
why SSH users sometimes fail on macOS,
and the exact steps to create a working SSH-only user.

---

## Big Picture

For a user to log in via SSH on macOS, ALL of the following must exist:

1. User record exists  
2. Home directory exists  
3. Valid login shell  
4. Authentication identity (ShadowHash / AuthenticationAuthority)  
5. Account is enabled  
6. User is allowed to use SSH  

Linux automatically creates most of these.
macOS often does NOT.

---

## Linux vs macOS Mental Model

### Linux

```bash
useradd bob
passwd bob
ssh bob@host  # works immediately
```

### macOS Reality

```md
1. create user
2. create auth identity
3. enable account
4. assign shell
5. allow ssh
6. then ssh works
```

- macOS has more security layers.

---

## 1) Create User (macOS)

```bash
sudo sysadminctl -addUser bob -password TEMP123
```

This ONLY creates a basic record.

## 2) Create Authentication Identity (MOST IMPORTANT)

```bash
sudo sysadminctl -resetPasswordFor bob -newPassword REALPASS
```

This creates:

- ShadowHash
- AuthenticationAuthority

Without this → SSH accepts password but disconnects.

Verify:

```bash
dscl . -read /Users/bob AuthenticationAuthority
```

Must contain: `;ShadowHash;`

## 3) Enable Account

```bash
sudo pwpolicy -u bob -enableuser
```

## 4) Ensure Login Shell

```bash
dscl . -read /Users/bob UserShell
```

Must be:

- `/bin/zsh` or
- `/bin/bash`

If not:

```bash
sudo dscl . -create /Users/bob UserShell /bin/zsh
```

## 5) Ensure Home Directory

```bash
dscl . -read /Users/bob NFSHomeDirectory
```

Expected:

/Users/bob
Fix if needed:

sudo dscl . -create /Users/bob NFSHomeDirectory /Users/bob
sudo chown bob:staff /Users/bob
sudo chmod 755 /Users/bob
6) Allow User To SSH
sudo dseditgroup -o edit -a bob -t user com.apple.access_ssh
Verify:

dseditgroup -o checkmember -m bob com.apple.access_ssh
Expected:

yes bob is a member of com.apple.access_ssh
7) Enable SSH Service

Terminal must have Full Disk Access.

Then:

```bash
sudo systemsetup -setremotelogin on
sudo systemsetup -getremotelogin
```

Expected: `Remote Login: On`

## 8) Test From Linux

```bash
ssh bob@MAC_IP
```

Success looks like:

```md
bob@MacBook-Pro ~ %
```

---

## Common Errors Explained

### Error: Password accepted, then connection closed

```md
Password:
Connection closed by port 22
```

**Meaning:**
User exists, but macOS cannot start login session.

Almost always caused by:

- Missing AuthenticationAuthority
- Account disabled
- Invalid shell

**Fix with:**

```bash
sudo sysadminctl -resetPasswordFor bob -newPassword PASS
sudo pwpolicy -u bob -enableuser
```

### Error: `su bob -> Sorry`

Normal on macOS. Apple blocks terminal user switching.

Use SSH or GUI login only.

### Error: `ms22-test not in sudoers`

Normal. Non-admin user.

### Error: `Operation not permitted (chown)`

Due to SIP (System Integrity Protection). Harmless.

---

## Golden Recipe (Always Works)

```bash
sudo sysadminctl -addUser USER -password TEMP
sudo sysadminctl -resetPasswordFor USER -newPassword REALPASS
sudo pwpolicy -u USER -enableuser
sudo dseditgroup -o edit -a USER -t user com.apple.access_ssh
sudo systemsetup -setremotelogin on
```

**Linux equivalent:**

```bash
useradd USER
passwd USER
```

---

## Conceptual Difference

### In Linux

- File-based
- Server-first
- Simple

### In macOS

- Directory-service based
- Desktop-first
- Multi-layer security

---

## Debug Checklist

```bash
id USER
dscl . -read /Users/USER AuthenticationAuthority
dscl . -read /Users/USER UserShell
dscl . -read /Users/USER NFSHomeDirectory
pwpolicy -u USER -getpolicy
dseditgroup -o checkmember -m USER com.apple.access_ssh
```

If all good → SSH will work.

---

## Recommended Practice

- Keep SSH user non-admin
- Use SSH keys
- Keep user hidden from GUI

---
