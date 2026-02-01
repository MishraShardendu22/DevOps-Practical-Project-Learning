# SSH, Containers, and Your Environment — Summary

## 1. Initial Observation

You attempted:

```bash
ssh root@172.28.0.12
````

and the connection hung.

From:

```bash
ip addr
```

you saw:

* 127.0.0.1 → loopback
* 172.28.0.12 → internal network IP

---

## 2. systemctl Failure

Command:

```bash
sudo systemctl status sshd
```

Output:

```
System has not been booted with systemd as init system (PID 1)
```

Meaning:

* systemd is not running
* therefore `systemctl` cannot work

This immediately indicates you are **not on a normal Linux VM**.

---

## 3. What Environment You Are In

You are inside a **container**, not a full virtual machine.

Execution stack:

Physical Server
→ Virtual Machine (provider-owned)
→ Container
→ Your shell

You only see the container layer.

---

## 4. What a Container Is

A container is:

* A lightweight isolated process
* With its own filesystem view
* Without a boot sequence
* Without an init system

It starts like:

```bash
docker run image command
```

No OS boot happens.

---

## 5. Why systemd Does Not Exist

systemd only exists when:

* Linux boots
* systemd becomes PID 1

Containers do not boot.

Therefore:

* No systemd
* No systemctl

---

## 6. Why No Background Services

Traditional OS:

boot → systemd → starts many services

Container:

start one program → when program exits, container dies

Containers intentionally avoid background daemons.

---

## 7. SSH Clarification

There are two separate things:

### SSH Client

Used to connect OUT:

```bash
ssh user@remote_machine
```

This **exists** in containers.

### SSH Server (sshd)

Used for others to connect IN:

```bash
ssh user@this_machine
```

This requires:

* sshd installed
* sshd running
* port 22 listening

Containers do **not** include sshd by default.

---

## 8. Why You Remember "Having SSH"

You previously used:

* SSH client

Not SSH server.

That allowed outbound connections, which still works.

---

## 9. Why SSH Into This Environment Fails

* No sshd installed
* No service manager
* Internal container IP (172.28.x.x)

Therefore:

```bash
ssh root@172.28.0.12
```

will hang.

---

## 10. Ephemeral Filesystem

Container storage:

* Temporary
* Deleted when container stops
* Reset on restart

Unless explicitly mounted.

---

## 11. Correct Access Method

Instead of SSH:

```bash
docker exec -it <container> bash
```

In Colab-like systems, you only use the notebook shell.

---

## 12. Final Truth

* You are inside a container
* Containers are not machines
* Containers do not run systemd
* Containers do not run sshd
* You can SSH out
* You cannot SSH in

---
