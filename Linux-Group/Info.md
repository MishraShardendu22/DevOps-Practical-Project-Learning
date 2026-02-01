# Linux User/Group, Network, Permissions, Archive, Text Processing Commands

---

## USER / GROUP MANAGEMENT

**useradd** – create user

```md
useradd john
useradd -m -s /bin/bash john
```

**passwd** – set/change password

```md
passwd john
```

**su** – switch user

```md
su john
su -
```

**cat /etc/passwd** – list users

```md
cat /etc/passwd
```

**userdel** – delete user

```md
userdel john
userdel -r john
```

**groupadd** – create group

```md
groupadd devs
```

**cat /etc/group** – list groups

```md
cat /etc/group
```

**gpasswd** – manage group members

```md
gpasswd -a john devs      # add user
gpasswd -M john,mary devs # set members
```

**groupdel** – delete group

```md
groupdel devs
```

---

## PERMISSIONS / OWNERSHIP

**dir-user-group-other** - file permission structure

```md
rwxr-xr--
user group other
```

**umask** – default permission mask

```md
umask
umask 022
```

**chown** – change owner

```md
chown john file.txt
chown john:devs file.txt
```

**chgrp** – change group

```md
chgrp devs file.txt
```

---

## ARCHIVE / TRANSFER

**zip** - create zip archive

```md
zip a.zip file.txt
zip -r a.zip folder/
```

**unzip** - extract zip archive

```md
unzip a.zip
```

**tar -xvzf** (extract)

```md
tar -xvzf a.tar.gz
```

**scp** – copy over SSH

```md
scp file.txt user@1.2.3.4:/tmp
```

**rsync** – sync files

```md
rsync -av dir/ user@host:/backup/
```

---

## WHAT IS net-tools

Legacy networking utilities package.
Provides: `ifconfig`, `netstat`, `route`, `arp`
Modern replacement: `iproute2` (`ip`, `ss`).

---

## NETWORK COMMANDS

**ping** - test connectivity

```md
ping google.com
```

**netstat** - network stats

```md
netstat -tulpn
```

**ifconfig** – interface info

```md
ifconfig
ifconfig eth0
```

* lo → loopback
* docker0 → docker bridge
* eth0 → ethernet

**traceroute** - trace route

```md
traceroute google.com
```

**tracepath** - trace path

```md
tracepath google.com
```

**mtr** – ping + trace

```md
mtr google.com
```

**nslookup** – DNS lookup

```md
nslookup google.com
```

**telnet** – test port

```md
telnet google.com 80
telnet google.com 443
```

**hostname** - show hostname/IP

```md
hostname
hostname -I
```

**ip** (replacement for ifconfig/route)

```md
ip a
ip r
```

**iwconfig** – wireless info

```md
iwconfig
```

**ss** – socket stats

```md
ss -tulpn
```

**whois** – domain info

```md
whois google.com
```

**nc (netcat)** – port test

```md
nc -zv google.com 80
```

**ifplugstatus** – cable plugged?

```md
ifplugstatus eth0
```

**curl** – HTTP request

```md
curl https://example.com
```

**jq** – JSON filter

```md
curl api | jq '.items[0].id'
```

**wget** – download

```md
wget https://site/file.zip
```

---

## ROUTE TABLES & IPTABLES

**Route table**
Controls where packets go.

```md
ip route
route -n
```

**iptables**
Firewall rules (allow/deny packets)

```md
iptables -L
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

(VPC routing video = cloud-level route tables between subnets, gateways, internet, NAT)

---

## MONITORING / DISCOVERY

**watch** – run command repeatedly

```md
watch -n 1 ip r
```

**nmap** – port scan

```md
nmap 1.2.3.4
nmap -p 80,443 host
```

**route** - show routing table

```md
route -n
```

---

## TEXT PROCESSING

**grep** – search

```md
grep root /etc/passwd
grep -i error log.txt
```

**awk** – column processing

```md
awk '{print $1}' file
awk -F: '{print $1}' /etc/passwd
```

**sed** – stream editor

```md
sed 's/old/new/' file
sed -i 's/old/new/g' file
```

---
