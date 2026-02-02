# Text Processing Commands - Done

## `cut`

```md
cut -d ":" -f1 /etc/passwd
```

---

## `sort`

```md
sort file.txt
sort -n numbers.txt
```

---

## `tee`

Write + display:

```md
echo hello | tee out.txt
```

Append:

```md
echo hi | tee -a out.txt
```

---

## `wc`

```md
wc file.txt
wc -l file.txt
```
