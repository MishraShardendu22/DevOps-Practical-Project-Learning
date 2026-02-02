# Session - Detail

Breakdown:

1. **mishrashardendu22 seat0**

   * Your graphical desktop session (GNOME/Wayland/X11)

2. **mishrashardendu22 tty2**

   * Your local virtual console (Ctrl+Alt+F2)

3. **root pts/3**

   * Root user logged in via a pseudo-terminal
   * Usually from: `sudo -i`, `su`, or an SSH session

So:

* **mishrashardendu22** → You (2 sessions)
* **root** → Administrative account (1 session)

No extra users. No compromise indicated.

```bash
mishrashardendu22@fedora:~$ uptime
 18:27:16 up 3 days,  4:26,  2 users,  load average: 0.23, 0.72, 0.82
mishrashardendu22@fedora:~$ who
mishrashardendu22 seat0        2026-01-30 14:03
mishrashardendu22 tty2         2026-01-30 14:03
```

---

**scp** – copy over SSH

```md
scp file.txt user@1.2.3.4:/tmp
```

**rsync** – sync files

```md
rsync -av dir/ user@host:/backup/
```
