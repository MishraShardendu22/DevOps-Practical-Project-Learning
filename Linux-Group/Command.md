# Linux Command

## cat /etc/passwd

`cat /etc/passwd` displays the contents of the system file `/etc/passwd`.

That file contains a list of **all user accounts** on the system and their basic properties.

Each line represents one user, in this format:

```md
username:x:UID:GID:comment:home_directory:login_shell
```

Example:

```md
root:x:0:0:root:/root:/bin/bash
```

Meaning:

* `root` → username
* `x` → password stored elsewhere (in `/etc/shadow`)
* `0` → user ID
* `0` → group ID
* `root` → description
* `/root` → home directory
* `/bin/bash` → default shell

---

