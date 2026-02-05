# 💾 Volume Management Guide

Comprehensive guide to volume management in Linux and AWS, covering block storage, file systems, and EBS volumes.

---

## 📚 Contents

### [Volume Management Info](Info.md)

Complete guide covering:
- Block storage concepts
- Linux volume management
- AWS EBS volumes
- Mounting and formatting
- Practical examples

---

## 🎯 Core Concepts

### 1. Block Storage

**Block of memory** = Raw storage as fixed-size blocks

Examples:
- `/dev/xvda` (AWS)
- `/dev/sda` (Linux)
- `/dev/nvme0n1` (NVMe drives)

**Characteristics**:
- No filesystem initially
- Requires formatting
- OS treats as disk

---

### 2. Volumes

**Volume** = Logical representation of block storage

**In AWS**:
- Volume = EBS Volume
- Network-attached storage
- Persistent across instance stops

**In Linux**:
- Appears as block device
- Can be formatted with filesystem
- Mounted to directory

---

### 3. EC2 & EBS Relationship

```
┌─────────────┐         attached to        ┌─────────────┐
│ EBS Volume  │ ─────────────────────────→ │ EC2 Instance│
│ (Storage)   │                            │   (VM)      │
└─────────────┘                            └─────────────┘
```

**EBS Benefits**:
- Persists after instance termination
- Can be detached and reattached
- Snapshots for backup
- Scalable size

---

## 🛠️ Common Operations

### Check Available Disks

```bash
# List all block devices
lsblk

# Detailed disk information
sudo fdisk -l

# Partition information
df -h
```

### Format a Volume

```bash
# Create ext4 filesystem
sudo mkfs.ext4 /dev/xvdf

# Create xfs filesystem
sudo mkfs.xfs /dev/xvdf
```

### Mount a Volume

```bash
# Create mount point
sudo mkdir /mnt/data

# Mount volume
sudo mount /dev/xvdf /mnt/data

# Verify mount
df -h | grep /mnt/data
```

### Permanent Mount (fstab)

```bash
# Get UUID
sudo blkid /dev/xvdf

# Edit fstab
sudo nano /etc/fstab

# Add line:
UUID=your-uuid-here /mnt/data ext4 defaults,nofail 0 2

# Test mount
sudo mount -a
```

### Unmount a Volume

```bash
# Unmount
sudo umount /mnt/data

# Force unmount (if busy)
sudo umount -f /mnt/data

# Lazy unmount
sudo umount -l /mnt/data
```

---

## 🔍 AWS EBS Specific

### Attach EBS Volume

1. **In AWS Console**:
   - Create EBS volume
   - Attach to EC2 instance
   - Note device name (e.g., `/dev/xvdf`)

2. **In EC2 Instance**:
   ```bash
   # Check if attached
   lsblk
   
   # Format (first time only)
   sudo mkfs.ext4 /dev/xvdf
   
   # Mount
   sudo mount /dev/xvdf /mnt/data
   ```

### Resize EBS Volume

```bash
# After increasing size in AWS Console

# Extend partition (if needed)
sudo growpart /dev/xvdf 1

# Resize filesystem
sudo resize2fs /dev/xvdf1  # for ext4
sudo xfs_growfs /mnt/data  # for xfs
```

### EBS Snapshots

```bash
# Create snapshot (AWS CLI)
aws ec2 create-snapshot \
  --volume-id vol-1234567890abcdef0 \
  --description "My snapshot"

# Restore from snapshot
# 1. Create volume from snapshot
# 2. Attach to instance
# 3. Mount as usual
```

---

## 📊 Filesystem Types

| Filesystem | Use Case | Pros | Cons |
|------------|----------|------|------|
| **ext4** | General purpose | Mature, reliable | Not latest features |
| **xfs** | Large files | High performance | Hard to shrink |
| **btrfs** | Advanced features | Snapshots, compression | Less mature |
| **tmpfs** | RAM disk | Very fast | Volatile |

---

## 🎯 Best Practices

### 1. Always Use UUID in fstab
```bash
# Good (survives device name changes)
UUID=xxx /mnt/data ext4 defaults 0 2

# Bad (device names can change)
/dev/xvdf /mnt/data ext4 defaults 0 2
```

### 2. Use nofail Option
```bash
# Prevents boot failure if volume unavailable
UUID=xxx /mnt/data ext4 defaults,nofail 0 2
```

### 3. Regular Backups
```bash
# For critical data
# - Use EBS snapshots
# - Use rsync to another volume
# - Use AWS Backup service
```

### 4. Monitor Disk Usage
```bash
# Check space
df -h

# Find large directories
du -sh /* | sort -h

# Set up alerts
# - CloudWatch for EBS
# - Monitoring tools
```

---

## 🔧 Troubleshooting

### Volume Not Showing

```bash
# Refresh device list
sudo partprobe

# Check kernel messages
dmesg | grep sd

# Check AWS attachment
aws ec2 describe-volumes --volume-ids vol-xxx
```

### Cannot Unmount (Device Busy)

```bash
# Find processes using volume
sudo lsof /mnt/data
sudo fuser -m /mnt/data

# Kill processes
sudo fuser -km /mnt/data

# Then unmount
sudo umount /mnt/data
```

### Mount Permission Denied

```bash
# Check filesystem errors
sudo fsck /dev/xvdf

# Check mount point permissions
ls -ld /mnt/data

# Fix permissions
sudo chmod 755 /mnt/data
```

---

## 📚 Related Topics

### Docker Volumes
See [Docker Volumes Guide](../Docker-Learning-Practical/07-Docker-Volumes.md)

Differences:
- **Linux Volumes**: OS-level block storage
- **Docker Volumes**: Container-level data persistence

---

## 🎓 Learning Path

1. ✅ Understand block storage concepts
2. ✅ Practice mounting/unmounting
3. ✅ Learn filesystem types
4. ✅ Work with AWS EBS
5. ✅ Implement backup strategies
6. ✅ Master troubleshooting

---

**Persistent storage mastery! 💾**
