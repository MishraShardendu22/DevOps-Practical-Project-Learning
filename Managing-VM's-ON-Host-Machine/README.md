# 🖥️ Managing VMs on Host Machine

Practical guide to server virtualization using VirtualBox, including network configuration, service deployment, and network analysis.

---

## 📚 Contents

### [VM Management Guide](Info.md)

Complete practical covering:
- VirtualBox VM setup
- Network configuration (NAT & Host-only)
- Service installation (Apache2, OpenSSH)
- Connectivity testing
- Network packet analysis with Wireshark

---

## 🎯 Objectives

This practical demonstrates:

1. **Server Virtualization** fundamentals
2. **Network Configuration** for VMs
3. **Service Deployment** in isolated environments
4. **Network Analysis** and troubleshooting

---

## 🛠️ Tools & Software

### Virtualization
- **VirtualBox** - Type 2 hypervisor
- **Ubuntu Server 22.04** - Guest OS

### Services
- **Apache2** - Web server
- **OpenSSH** - Remote access

### Network Tools
- **Wireshark** - Packet capture and analysis
- **ping** - Connectivity testing
- **ssh** - Remote connection

---

## 📐 Network Architecture

### Network Modes Used

```
┌─────────────────────────────────────────────────┐
│                  Host Machine                    │
│                                                  │
│  ┌────────────┐              ┌────────────┐    │
│  │   VM1      │              │   VM2      │    │
│  │  (Server)  │◄────────────►│  (Client)  │    │
│  │            │  Host-only   │            │    │
│  └────────────┘   Network    └────────────┘    │
│       │                            │            │
│       │ NAT                        │ NAT        │
│       ▼                            ▼            │
│  ┌────────────────────────────────────────┐    │
│  │         Internet (via NAT)             │    │
│  └────────────────────────────────────────┘    │
└─────────────────────────────────────────────────┘
```

### NAT Network
- **Purpose**: Internet access for VMs
- **VM → Internet**: ✅ Yes
- **Host → VM**: ❌ No (without port forwarding)
- **VM ↔ VM**: ❌ No

### Host-only Network
- **Purpose**: VM-to-VM and Host-to-VM communication
- **VM ↔ VM**: ✅ Yes
- **Host ↔ VM**: ✅ Yes
- **VM → Internet**: ❌ No (isolated)

---

## 🚀 Setup Procedure

### 1. VM Creation

```bash
# Created two VMs with:
# - Ubuntu Server 22.04
# - 2 GB RAM minimum
# - 10 GB disk space
# - Two network adapters each
```

### 2. Network Configuration

**Adapter 1 (NAT)**:
- Automatic DHCP
- Internet access

**Adapter 2 (Host-only)**:
```bash
# VM1 (Server) - Static IP
sudo nano /etc/netplan/00-installer-config.yaml

network:
  ethernets:
    enp0s8:  # Host-only adapter
      addresses:
        - 192.168.56.10/24
  version: 2

# Apply configuration
sudo netplan apply
```

```bash
# VM2 (Client) - Static IP
network:
  ethernets:
    enp0s8:
      addresses:
        - 192.168.56.11/24
  version: 2

sudo netplan apply
```

### 3. Service Installation

**On VM1 (Server)**:

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Apache2
sudo apt install apache2 -y

# Install OpenSSH Server
sudo apt install openssh-server -y

# Verify services
sudo systemctl status apache2
sudo systemctl status ssh
```

### 4. Connectivity Testing

**Ping Test** (VM1 ↔ VM2):
```bash
# From VM2
ping 192.168.56.10

# Should see:
# 64 bytes from 192.168.56.10: icmp_seq=1 ttl=64 time=0.5 ms
```

**SSH Test** (Host → VM1):
```bash
# From host machine
ssh username@192.168.56.10

# Should connect successfully
```

**Web Access** (VM2 → VM1):
```bash
# From VM2 browser or curl
curl http://192.168.56.10

# Should see Apache default page
```

### 5. Network Analysis with Wireshark

**Capture HTTP Traffic**:
```bash
# On host machine:
# 1. Open Wireshark
# 2. Capture on Host-only adapter
# 3. Filter: http
# 4. Access Apache from VM2
# 5. Observe HTTP GET request and response
```

**Capture SSH Traffic**:
```bash
# Filter: tcp.port == 22
# SSH from host to VM1
# Observe encrypted traffic
```

**Capture ICMP (Ping)**:
```bash
# Filter: icmp
# Ping between VMs
# Observe echo request/reply
```

---

## 📊 Observations

### Network Performance
- **Latency**: < 1ms between VMs (host-only network)
- **Throughput**: Near-native performance
- **Reliability**: Stable connectivity

### Service Accessibility
- ✅ Apache accessible on port 80
- ✅ SSH accessible on port 22
- ✅ Services respond correctly

### Network Isolation
- ✅ VMs can access internet (NAT)
- ✅ VMs can communicate (host-only)
- ✅ Proper network segmentation

---

## 🔍 Packet Analysis Results

### HTTP Traffic Pattern
```
Client → Server: GET / HTTP/1.1
Server → Client: HTTP/1.1 200 OK
                  [HTML content]
```

### SSH Traffic Pattern
```
Client → Server: TCP SYN (port 22)
Server → Client: TCP SYN-ACK
Client → Server: TCP ACK
[Encrypted SSH session]
```

### ICMP Traffic Pattern
```
Source → Dest: Echo Request (type 8)
Dest → Source: Echo Reply (type 0)
Round-trip time: ~0.5ms
```

---

## 🎯 Key Learnings

### Virtualization Benefits
1. **Isolation** - Separate test environments
2. **Flexibility** - Multiple OS configurations
3. **Safety** - Snapshots and rollback
4. **Cost-effective** - No additional hardware

### Network Configuration
1. **NAT** for internet access
2. **Host-only** for internal communication
3. **Static IPs** for predictable addressing
4. **Multiple adapters** for different purposes

### Service Deployment
1. **Easy installation** in isolated environment
2. **Testing without risk** to production
3. **Network service verification**
4. **Security testing** capabilities

---

## 🛡️ Security Considerations

### Best Practices

1. **Firewall Configuration**
   ```bash
   sudo ufw enable
   sudo ufw allow ssh
   sudo ufw allow http
   ```

2. **SSH Hardening**
   ```bash
   # Edit /etc/ssh/sshd_config
   PermitRootLogin no
   PasswordAuthentication no  # Use keys
   ```

3. **Keep Systems Updated**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

4. **Network Isolation**
   - Use host-only for sensitive services
   - NAT for internet-facing services

---

## 🔧 Troubleshooting

### VM Cannot Access Internet

```bash
# Check NAT adapter
ip addr show

# Verify default gateway
ip route show

# Test DNS
ping 8.8.8.8
ping google.com
```

### VMs Cannot Ping Each Other

```bash
# Check host-only network
VBoxManage list hostonlyifs

# Verify IP configuration
ip addr show

# Check netplan config
sudo netplan apply

# Restart network
sudo systemctl restart systemd-networkd
```

### Service Not Accessible

```bash
# Check service status
sudo systemctl status apache2
sudo systemctl status ssh

# Check listening ports
sudo netstat -tlnp
sudo ss -tlnp

# Check firewall
sudo ufw status
```

---

## 📚 Related Topics

- [Docker Networking](../Docker-Learning-Practical/06-Docker-Network.md) - Container networking concepts
- [Linux Networking](../Commands-Linux/ssh-network.md) - Network commands
- [SSH Configuration](../SSH-Practical/) - Secure remote access

---

## 🎓 Next Steps

After mastering VM management:

1. **Advanced Networking**
   - Bridged network mode
   - Internal network mode
   - Port forwarding

2. **VM Automation**
   - Vagrant for VM provisioning
   - Ansible for configuration
   - Scripts for setup

3. **Container Migration**
   - Convert VMs to containers
   - Docker vs VM comparison
   - Kubernetes orchestration

4. **Cloud Platforms**
   - AWS EC2 instances
   - Azure VMs
   - Google Compute Engine

---

## 📄 Additional Files

- **[experiment.pcap](experiment.pcap)** - Wireshark capture file with sample traffic

---

**Master virtualization! 🖥️**
