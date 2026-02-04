# Docker Installation & Setup

## Installation on Linux (Fedora/RHEL)

### Method 1: Using DNF Package Manager

```bash
# Remove old versions
sudo dnf remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-selinux \
                docker-engine-selinux \
                docker-engine

# Add Docker repository
sudo dnf -y install dnf-plugins-core
sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo

# Install Docker Engine
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker

# Verify installation
sudo docker run hello-world
```

### Method 2: Docker Desktop for Linux

```bash
# Download Docker Desktop
# Visit: https://docs.docker.com/desktop/install/linux-install/

# Install the package
sudo dnf install ./docker-desktop-<version>-<arch>.rpm

# Start Docker Desktop
systemctl --user start docker-desktop
```

### Add User to Docker Group (Avoid using sudo)

```bash
# Add current user to docker group
sudo usermod -aG docker $USER

# Activate changes (or logout/login)
newgrp docker

# Verify
docker run hello-world
```

## Installation on Ubuntu/Debian

```bash
# Update package index
sudo apt-get update

# Install dependencies
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Add Docker's official GPG key
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Set up repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Verify installation
sudo docker run hello-world
```

## Installation on AWS EC2

### Launch EC2 Instance

1. Choose Amazon Linux 2 or Ubuntu AMI
2. Select instance type (t2.micro for testing)
3. Configure security group to allow necessary ports
4. Launch instance

### Install Docker on Amazon Linux 2

```bash
# Connect to EC2 instance
ssh -i your-key.pem ec2-user@your-instance-ip

# Update packages
sudo yum update -y

# Install Docker
sudo yum install docker -y

# Start Docker service
sudo service docker start

# Enable Docker to start on boot
sudo systemctl enable docker

# Add ec2-user to docker group
sudo usermod -a -G docker ec2-user

# Logout and login again
exit
# SSH back in

# Verify installation
docker info
```

### Install Docker on Ubuntu EC2

```bash
# Connect to instance
ssh -i your-key.pem ubuntu@your-instance-ip

# Follow Ubuntu installation steps above
# Then verify
docker run hello-world
```

## Docker Login Credential Configuration (Linux)

### The Problem

On Linux, Docker Desktop uses credential helpers to store login credentials. The default helper is `pass` (Unix password store), which requires a GPG key.

### Root Cause

Docker Desktop **does not store credentials itself** — it delegates to the OS.

On **Linux**, Docker uses:

- **Credential helper**: `pass` (Unix password store)
- **Requirement**: GPG key must exist
- **If no GPG key exists** → Docker cannot save credentials → login fails

### Why This Happens on Linux (Not Mac/Windows)

| OS | Credential Backend | Manual Setup Needed? |
|----|--------------------|----------------------|

| Windows | Windows Credential Manager | ❌ No |
| macOS | Keychain | ❌ No |
| Linux | pass / secretservice | ✅ Yes (first time) |

**Reason**: Linux intentionally avoids centralized proprietary vaults. It expects users to own their crypto keys.

### Why Docker Doesn't Auto-Create Keys

**Security model:**

- Auto-creating crypto keys without user consent = bad security practice
- GPG keys are personal identity objects
- Users must explicitly generate them
- Docker follows OS security policy

### Solution 1: Set Up GPG and pass (Recommended)

#### Step 1: Generate GPG Key

```bash
# Generate a new GPG key
gpg --full-generate-key

# Follow prompts:
# - Select: (1) RSA and RSA
# - Key size: 4096
# - Expiration: 0 (never expires) or set your preference
# - Enter your name and email
# - Set a passphrase (remember it!)
```

#### Step 2: Get Your Key ID

```bash
# List your GPG keys
gpg --list-secret-keys --keyid-format=long

# Output example:
# /home/user/.gnupg/pubring.kbx
# -------------------------------
# sec   rsa4096/ABCD1234EFGH5678 2024-01-01 [SC]
# uid                 [ultimate] Your Name <your.email@example.com>

# Copy the key ID (e.g., ABCD1234EFGH5678)
```

#### Step 3: Initialize pass

```bash
# Initialize pass with your GPG key ID
pass init ABCD1234EFGH5678

# You should see:
# mkdir: created directory '/home/user/.password-store/'
# Password store initialized for ABCD1234EFGH5678
```

#### Step 4: Restart Docker Desktop

```bash
# Restart Docker Desktop
systemctl --user restart docker-desktop

# Or restart the Docker daemon
sudo systemctl restart docker
```

#### Step 5: Login to Docker

```bash
# Now login should work
docker login

# Or with personal access token
docker login -u your-username
# Enter your PAT as password
```

### Solution 2: Disable Credential Store (Not Recommended)

If you want to skip encryption entirely:

```bash
# Edit Docker config
nano ~/.docker/config.json

# Set credsStore to empty
{
  "credsStore": ""
}

# Save and restart Docker Desktop
systemctl --user restart docker-desktop
```

**⚠️ Warning**: This stores credentials in plaintext. Only use for testing/learning.

### Solution 3: Use Alternative Credential Helper

```bash
# Install pass as alternative
sudo dnf install pass  # Fedora
sudo apt install pass  # Ubuntu

# Or use gnome-keyring
sudo dnf install gnome-keyring
```

## Verify Installation

```bash
# Check Docker version
docker --version
docker version

# Check Docker info
docker info

# Run test container
docker run hello-world

# Run docker
sudo systemctl start docker

# Check if daemon is running
sudo systemctl status docker

# Stop docker 
sudo systemctl stop docker
```

## Common Post-Installation Issues

### Issue 1: Permission Denied

```bash
# Error: Got permission denied while trying to connect to Docker daemon
# Solution: Add user to docker group
sudo usermod -aG docker $USER
newgrp docker
```

### Issue 2: Docker Daemon Not Running

```bash
# Start Docker daemon
sudo systemctl start docker

# Enable on boot
sudo systemctl enable docker
```

### Issue 3: Cannot Connect to Docker Daemon

```bash
# Check if daemon is running
sudo systemctl status docker

# Check socket
ls -l /var/run/docker.sock

# Restart daemon
sudo systemctl restart docker
```

## Uninstall Docker

### On Fedora/RHEL

```bash
# Stop Docker
sudo systemctl stop docker

# Remove packages
sudo dnf remove docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Remove data
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

### On Ubuntu

```bash
# Remove packages
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Remove data
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

## Next Steps

After successful installation:

1. ✅ Verify Docker is running
2. ✅ Configure credentials (on Linux)
3. ✅ Login to Docker Hub
4. ✅ Try pulling and running images
5. ✅ Learn Docker commands (see [04-commands.md](./04-commands.md))

## Summary

- Docker can be installed via package managers or Docker Desktop
- Linux requires additional credential helper setup (GPG + pass)
- Always verify installation with `docker run hello-world`
- Add user to docker group to avoid using sudo
- AWS EC2 installation is straightforward with package managers
