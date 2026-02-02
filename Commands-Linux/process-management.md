# Process Management Commands - Done

## `top`

```md
top
```

Quit:

```md
q
```

---

## `htop`

```md
htop
```

if missing -

```md
sudo apt install htop
```

---

## `ps`

```md
ps
ps aux
```

---

## `fuser`

Find process using file:

```md
fuser file.txt
```

Find who uses port 80:

```md
fuser -n tcp 80
```

Kill:

```md
fuser -k 80/tcp
```
