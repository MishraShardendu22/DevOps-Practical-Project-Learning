# 👥 Linux User & Group Management

Comprehensive guide to managing users, groups, permissions, and related system administration tasks in Linux.

---

## 📚 Contents

### [User/Group Management Guide](Info.md)

Complete reference covering:

- User creation and management
- Group management
- File permissions
- Network utilities
- Archive operations
- Text processing
- System monitoring

---

## 🎯 Core Topics

### User Management

Create, modify, and delete user accounts:

```bash
# Create user
useradd john
useradd -m -s /bin/bash john

# Set password
passwd john

# Switch user
su john
su -

# Delete user
userdel john
userdel -r john  # Remove home directory
```

### Group Management

Manage user groups and permissions:

```bash
# Create group
groupadd devs

# Add user to group
usermod -aG devs john

# List user groups
groups john

# View all groups
cat /etc/group
```

---

## 🔐 Permission Management

### Understanding Permissions

```
-rwxr-xr--
│││││││││└─ Others: read
││││││││└── Others: execute (none)
│││││││└─── Group: read
││││││└──── Group: execute
│││││└───── Group: write (none)
││││└────── User: read
│││└─────── User: write
││└──────── User: execute
│└───────── File type (- = file, d = directory)
```

### Changing Permissions

```bash
# Symbolic mode
chmod u+x file.sh      # Add execute for user
chmod g-w file.txt     # Remove write for group
chmod o+r file.txt     # Add read for others

# Numeric mode
chmod 755 script.sh    # rwxr-xr-x
chmod 644 file.txt     # rw-r--r--
chmod 600 private.key  # rw-------
```

### Changing Ownership

```bash
# Change owner
chown john file.txt

# Change owner and group
chown john:devs file.txt

# Recursive change
chown -R john:devs /var/www
```

---

## 📊 Common Permission Patterns

| Permission | Numeric | Use Case |
|------------|---------|----------|
| `rwxr-xr-x` | 755 | Executables, scripts |
| `rw-r--r--` | 644 | Regular files |
| `rw-------` | 600 | Private files (SSH keys) |
| `rwxrwxrwx` | 777 | World writable (avoid!) |
| `rwx------` | 700 | Private directories |

---

## 🛠️ User Information

### View User Details

```bash
# List all users
cat /etc/passwd

# Current user info
id
whoami

# User groups
groups

# Detailed user info
finger username
getent passwd username
```

### Login Information

```bash
# Last logins
last

# Current logged in users
who
w

# Login history
lastlog
```

---

## 🔧 Advanced User Management

### Modify User Account

```bash
# Change shell
usermod -s /bin/zsh john

# Change home directory
usermod -d /new/home john

# Lock user account
usermod -L john

# Unlock user account
usermod -U john

# Set expiration date
usermod -e 2024-12-31 john
```

### Password Policies

```bash
# Force password change on next login
passwd -e john

# Set password expiry
chage -M 90 john       # Max 90 days
chage -m 7 john        # Min 7 days
chage -W 14 john       # Warn 14 days before

# View password info
chage -l john
```

---

## 👨‍💼 Sudo Configuration

### Grant Sudo Access

```bash
# Add user to sudo group (Debian/Ubuntu)
usermod -aG sudo john

# Add user to wheel group (RHEL/Fedora)
usermod -aG wheel john

# Edit sudoers file
visudo

# Example sudoers entry:
john ALL=(ALL:ALL) ALL
```

### Sudoers Examples

```bash
# User can run all commands
john ALL=(ALL) ALL

# User can run without password
john ALL=(ALL) NOPASSWD: ALL

# User can run specific commands
john ALL=(ALL) /bin/systemctl, /usr/bin/docker

# Group-based sudo
%devs ALL=(ALL) ALL
```

---

## 📁 Special Permissions

### SUID (Set User ID)

```bash
# Set SUID bit
chmod u+s /usr/bin/program
chmod 4755 /usr/bin/program

# File executes with owner's permissions
```

### SGID (Set Group ID)

```bash
# Set SGID bit on file
chmod g+s /usr/bin/program
chmod 2755 /usr/bin/program

# Set SGID on directory (inherit group)
chmod g+s /shared/directory
```

### Sticky Bit

```bash
# Set sticky bit (only owner can delete)
chmod +t /tmp
chmod 1777 /tmp

# Common for shared directories
```

---

## 🎯 Best Practices

### Security

1. **Principle of Least Privilege**
   - Grant minimum necessary permissions
   - Use groups for role-based access

2. **Protect Sensitive Files**
   ```bash
   chmod 600 ~/.ssh/id_rsa
   chmod 644 ~/.ssh/id_rsa.pub
   chmod 700 ~/.ssh
   ```

3. **Regular Audits**
   ```bash
   # Find files with SUID
   find / -perm -4000 2>/dev/null
   
   # Find world-writable files
   find / -perm -002 2>/dev/null
   ```

### User Management

1. **Use Strong Passwords**
   ```bash
   # Install pwgen
   pwgen -s 16 1
   ```

2. **Disable Unused Accounts**
   ```bash
   usermod -L olduser
   ```

3. **Monitor Login Activity**
   ```bash
   last | head -20
   lastb | head -10  # Failed logins
   ```

---

## 🔍 Troubleshooting

### Permission Denied

```bash
# Check file permissions
ls -l file.txt

# Check user groups
groups

# Check directory permissions
namei -l /path/to/file
```

### Cannot sudo

```bash
# Check sudo group membership
groups username | grep -E 'sudo|wheel'

# Check sudoers file
sudo visudo -c
```

### User Cannot Login

```bash
# Check account status
passwd -S username

# Check login shell
getent passwd username

# Check account lock status
usermod -U username
```

---

## 📚 Related Topics

- [Linux Commands](../Commands-Linux/)
- [SSH Configuration](../SSH-Practical/)
- [System Management](../Commands-Linux/system-info.md)

---

## 🎓 Learning Path

1. ✅ Understand users and groups
2. ✅ Master permission model (rwx)
3. ✅ Learn numeric permissions
4. ✅ Practice user creation
5. ✅ Configure sudo access
6. ✅ Implement security best practices

---

**Secure user management! 🔐**
