# Identity & Service Management: macOS vs Linux

## 1. Conceptual Layers

Every Unix-like OS separates administration into layers:

```
Privilege Layer      → who is allowed
Identity Layer       → who exists
Service Layer        → what runs
```

| Layer     | Purpose                     |
| --------- | --------------------------- |
| Privilege | Control execution authority |
| Identity  | Manage users/groups         |
| Service   | Manage background programs  |

---

## 2. Privilege Layer

### sudo (macOS & Linux)

Purpose:

```
Execute a command as another user (usually root)
```

Characteristics:

* Does NOT manage users
* Does NOT edit databases
* Only elevates permission

Example:

```
sudo ls /root
```

---

## 3. Identity Layer (User & Group Management)

### macOS

Primary tool:

```
dscl  (Directory Services Command Line)
```

Backend:

```
opendirectoryd
```

Data stored as directory records:

```
Users
Groups
UIDs
GIDs
Shells
Home directories
Passwords
```

Basic operations:

```
dscl . list /Users
dscl . read /Users/john
dscl . create /Users/john
dscl . passwd /Users/john
dscl . delete /Users/john
```

Behavior:

* Direct database editor
* No validation
* Extremely powerful

Usually combined with sudo:

```
sudo dscl ...
```

---

### Linux

No single unified tool.

Local users stored in:

```
/etc/passwd
/etc/shadow
/etc/group
```

Common tools:

```
useradd
usermod
userdel
groupadd
groupdel
passwd
getent
```

Examples:

```
useradd john
usermod -s /bin/bash john
passwd john
getent passwd
```

Network directory (LDAP/AD):

```
ldapadd
ldapmodify
ldapsearch
realm
adcli
sssctl
```

---

### Identity Mapping

| macOS | Linux                                 |
| ----- | ------------------------------------- |
| dscl  | useradd/usermod/userdel/passwd/getent |

---

## 4. Service Layer (Background Programs)

### Linux

Service manager:

```
systemd
```

Controller:

```
systemctl
```

Functions:

```
start   → run now
stop    → stop now
restart → restart now
enable  → start at boot
disable → do not start at boot
status  → show state
```

Example:

```
systemctl start nginx
systemctl enable nginx
```

Meaning:

```
Run nginx now
Run nginx automatically after boot
```

systemctl does NOT:

* Create programs
* Compile software
* Manage users

---

### macOS Equivalent

Service manager:

```
launchd
```

Controller:

```
launchctl
```

---

### Service Mapping

| macOS     | Linux     |
| --------- | --------- |
| launchctl | systemctl |

---

## 5. Running Services Without systemctl

Linux does NOT require systemd.

Options:

### Direct Execution

```
/usr/sbin/nginx
```

### SysVinit Scripts

```
/etc/init.d/nginx start
service nginx start
```

### Alternative Init Systems

OpenRC:

```
rc-service nginx start
```

runit:

```
sv start nginx
```

s6:

```
s6-svc -u /run/service/nginx
```

### Cron

```
@reboot /usr/sbin/nginx
```

systemctl is a convenience layer, not a requirement.

---

## 6. Cross-Platform Mental Model

### macOS

```
sudo        → privilege
dscl        → identity
launchctl   → services
```

### Linux

```
sudo        → privilege
useradd/usermod/passwd → identity
systemctl  → services
```

---
