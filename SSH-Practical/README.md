# 🔐 SSH Practical Guide

Practical SSH session management, configuration, and best practices for secure remote connections.

---

## 📚 Contents

### [Session Management](Session.md)

Understanding and managing SSH sessions:

- **Session Types**
  - Graphical desktop sessions (seat0)
  - Virtual console sessions (tty)
  - Pseudo-terminal sessions (pts)

- **User Identification**
  - Checking active sessions with `who`
  - System uptime with `uptime`
  - Identifying root vs user sessions

- **File Transfer**
  - `scp` - Secure copy over SSH
  - `rsync` - Efficient file synchronization

---

## 🚀 Quick Reference

### Check Active Sessions

```bash
# View all logged-in users
who

# View system uptime and load
uptime

# Detailed session information
w
```

### Secure File Transfer

```bash
# Copy file to remote server
scp file.txt user@server:/path/to/destination

# Copy from remote to local
scp user@server:/path/to/file.txt ./

# Sync directories
rsync -av local-dir/ user@server:/remote-dir/
```

### SSH Connection

```bash
# Basic connection
ssh user@server

# Connection with specific port
ssh -p 2222 user@server

# Connection with key
ssh -i ~/.ssh/key.pem user@server
```

---

## 🔍 Session Types Explained

### seat0 (Graphical Desktop)
- Your graphical login session
- GNOME/Wayland/X11 display
- Main interactive session

### tty (Virtual Console)
- Local terminal session
- Access via Ctrl+Alt+F2, F3, etc.
- Text-based interface

### pts (Pseudo-Terminal)
- Terminal via SSH or terminal emulator
- Created for remote sessions
- Most common for remote work

---

## 🛡️ Security Best Practices

### SSH Configuration

1. **Use Key-Based Authentication**
   ```bash
   # Generate SSH key
   ssh-keygen -t ed25519 -C "your_email@example.com"
   
   # Copy to server
   ssh-copy-id user@server
   ```

2. **Disable Password Authentication**
   ```bash
   # Edit /etc/ssh/sshd_config
   PasswordAuthentication no
   PubkeyAuthentication yes
   ```

3. **Change Default Port**
   ```bash
   # Edit /etc/ssh/sshd_config
   Port 2222
   ```

4. **Disable Root Login**
   ```bash
   # Edit /etc/ssh/sshd_config
   PermitRootLogin no
   ```

---

## 📖 Common Tasks

### Monitor Sessions

```bash
# List all users
who

# Detailed user info
w

# Last login information
last

# Currently logged in users
users
```

### Session Management

```bash
# Switch to another user
su - username

# Become root (requires sudo)
sudo -i

# End current session
exit

# Disconnect SSH but keep process running
# Use screen or tmux
```

---

## 🎯 Learning Objectives

After studying this guide, you should understand:

- ✅ Different types of Linux sessions
- ✅ How to identify active users and sessions
- ✅ Secure file transfer methods
- ✅ SSH security best practices
- ✅ Session monitoring and management

---

## 📚 Related Topics

- [Linux Commands](../Commands-Linux/)
- [Network Commands](../Commands-Linux/ssh-network.md)
- [User Management](../Linux-Group/)

---

**Stay secure! 🔒**
