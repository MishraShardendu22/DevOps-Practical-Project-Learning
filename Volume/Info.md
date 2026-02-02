# Volume Management in Linux and AWS

## 1. Block of Memory (Block Storage)

A **block of memory** (block device) is raw storage presented as fixed-size blocks.

Examples:

* `/dev/xvda`
* `/dev/xvdf`
* `/dev/sda`

Characteristics:

* No filesystem initially
* OS treats it like a disk
* Must be formatted before use

---

## 2. Volume

A **volume** is a logical representation of block storage.

In AWS:

* Volume = EBS Volume
  In Linux:
* Appears as block device (`/dev/xvdf`)

---

## 3. EC2 and EBS

### **EC2**

* Virtual machine

### **EBS**

* Network attached block storage
* Persists after instance stop
* Can be attached/detached

Relationship:

```md
EBS Volume  ---> attached to --->  EC2 Instance
```

---

## 4. Root Volume in EC2 (`/dev/xvda`)

* Boot disk
* Contains OS
* Usually mapped as:

```md
/dev/xvda  or  /dev/xvda1
```

---

## 5. lsblk

Lists block devices.

```md
lsblk
```

Example:

```md
NAME    SIZE TYPE MOUNTPOINT
xvda    20G  disk
└─xvda1 20G  part /
xvdf    10G  disk
```

---

## 6. df -h

Shows mounted filesystems and usage.

```md
df -h
```

---

## 7. Creating Volume Using EBS

From AWS console:

* EBS → Create Volume
* Choose size, type, AZ

No Linux command involved.

---

## 8. Attaching Volume to EC2 Instance

From AWS console:

* EBS → Actions → Attach Volume
* Choose instance
* Device name: `/dev/sdf`

Inside Linux it appears as:

```md
/dev/xvdf
```

---

## 9. Naming Convention of Volume in AWS

AWS Name → Linux Name

```md
/dev/sdf  -> /dev/xvdf
/dev/sdg  -> /dev/xvdg
```

NVMe based instances:

```md
/dev/nvme1n1
```

Always verify using:

```md
lsblk
```

---

## 10. Attach vs Mount (Critical Difference)

### **Attach**

* AWS level
* Makes disk visible to OS

### **Mount**

* OS level
* Makes filesystem accessible

Attach ≠ usable
Mount = usable

---

## 11. Process of Mounting (Normal Disk)

### Step 1: Verify disk

```md
lsblk
```

### Step 2: Create filesystem

```md
mkfs.ext4 /dev/xvdf
```

### Step 3: Create mount point

```md
mkdir /data
```

### Step 4: Mount

```md
mount /dev/xvdf /data
```

### Step 5: Verify

```md
df -h
```

---

## 12. umount

```md
umount /data
```

or

```md
umount /dev/xvdf
```

---

## 13. Physical Volume, Volume Group, Logical Volume

LVM layers:

```md
Disk --> Physical Volume --> Volume Group --> Logical Volume --> Filesystem --> Mount
```

Meaning:

### **Physical Volume (PV)**

* Disk initialized for LVM

### **Volume Group (VG)**

* Pool of PVs

### **Logical Volume (LV)**

* Virtual partition carved from VG

---

## 14. LVM

Logical Volume Manager
Provides:

* Resize
* Pooling
* Online expansion

---

## 15. pvs

Shows physical volumes.

```md
pvs
```

---

## 16. pvcreate

Convert disk to PV.

```md
pvcreate /dev/xvdf
```

---

## 17. vgcreate (not in list but mandatory)

Create volume group.

```md
vgcreate vgdata /dev/xvdf
```

---

## 18. lvcreate

Create logical volume.

```md
lvcreate -L 5G -n lvdata vgdata
```

Result device:

```md
/dev/vgdata/lvdata
```

---

## 19. mkfs.ext4

Create filesystem.

```md
mkfs.ext4 /dev/vgdata/lvdata
```

---

## 20. Mounting Logical Volume (IMPORTANT CRUX)

### Create directory

```md
mkdir /lvmdata
```

### Mount

```md
mount /dev/vgdata/lvdata /lvmdata
```

### Verify

```md
df -h
```

---

## 21. Mounting Physical Volume (IMPORTANT CRUX)

You **cannot mount PV directly**.

This is WRONG:

```md
mount /dev/xvdf /data   (after pvcreate)
```

Once disk becomes PV → must go through:

```md
PV -> VG -> LV -> mkfs -> mount
```

If you want direct mounting:

* Do NOT use pvcreate
* Just mkfs and mount

---

## 22. Disk Mount vs LV Mount

| Type       | Mounted Device     |
| ---------- | ------------------ |
| Disk Mount | /dev/xvdf          |
| LV Mount   | /dev/vgdata/lvdata |

Both use:

```md
mount <device> <directory>
```

Difference is device layer.

---

## 23. lvs

```md
lvs
```

Shows logical volumes.

---

## 24. lvdisplay

```md
lvdisplay
```

Detailed LV info.

---

## 25. Dynamic Storage (Online Expansion)

Add new space without unmounting.

---

## 26. lvextend

### Extend LV by 5G

```md
lvextend -L +5G /dev/vgdata/lvdata
```

### Resize filesystem

```md
resize2fs /dev/vgdata/lvdata
```

---

## 27. Expanding With New Disk (Typical Flow)

1. Attach new EBS
2. lsblk
3. pvcreate /dev/xvdg
4. vgextend vgdata /dev/xvdg
5. lvextend -L +10G /dev/vgdata/lvdata
6. resize2fs /dev/vgdata/lvdata

---

## 28. Final Mental Model (Memorize)

### For Physical Disk Mount

```md
Attach -> lsblk -> mkfs -> mount
```

### For LVM Mount

```md
Attach -> lsblk -> pvcreate -> vgcreate/vgextend -> lvcreate/lvextend -> mkfs -> mount
```

---
