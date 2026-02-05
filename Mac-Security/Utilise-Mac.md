# Remote Mac Execution from Linux via VS Code (SSH-Only Model)

This document defines the **only correct architecture** for:

> Editing on Linux → Executing on Mac hardware  
> Without touching the Mac physically

No monitor.  
No keyboard.  
No GUI.  
No login.

Only SSH.

---

## Architecture

```md

VS Code (Linux UI)
↓
SSH tunnel
↓
VS Code Server running on Mac
↓
Mac CPU / RAM / GPU executes everything

````

VS Code runs locally.  
VS Code Server runs on Mac.  
All compute happens on Mac.

---

## Prerequisites

On Linux:

```bash
sudo apt install openssh-client
````

On Mac (once):

```bash
sudo systemsetup -setremotelogin on
```

Confirm Mac reachable:

```bash
ssh user@mac_ip
```

If this works, everything works.

---

## Step 1 — Install VS Code Extension (Linux)

Open VS Code → Extensions

Install:

```md
Remote - SSH
```

---

## Step 2 — Add Mac as SSH Host

VS Code:

```md
Ctrl + Shift + P
Remote-SSH: Connect to Host
Add New Host
ssh user@mac_ip
```

Save to default config.

Then:

```md
Remote-SSH: Connect to Host
user@mac_ip
```

VS Code will:

* Upload VS Code Server to Mac
* Start server
* Open remote window

No Mac interaction.

---

## Step 3 — Open Folder ON MAC

Inside **remote** VS Code window:

```md
File → Open Folder → /Users/user
```

Create a directory:

```md
/Users/user/workspace
```

Open it.

---

## Step 4 — Copy Project (One Time)

From Linux terminal:

```bash
scp -r ~/Coding_Stuff_Fedora/DevOps-Learning-Final user@mac_ip:/Users/user/workspace/
```

After this, you never copy again unless you want.

---

## Step 5 — Work Normally

Inside remote VS Code:

* Edit files
* Use integrated terminal

Examples:

```bash
python main.py
make
docker build .
npm run dev
```

Execution happens on Mac.

---

## Step 6 — Verify You Are On Mac

Inside VS Code terminal:

```bash
uname
```

Expected:

```md
Darwin
```

CPU:

```bash
uname -m
```

Intel:

```md
x86_64
```

Apple Silicon:

```md
arm64
```

---

## Mental Model (Important)

* VS Code UI → Linux
* VS Code Server → Mac
* Terminal → Mac
* Python → Mac
* Docker → Mac
* GPU → Mac

Linux does **zero** compute.

---

## Zero-VS-Code Alternative (Pure Terminal)

Sync:

```bash
rsync -av project/ user@mac_ip:/Users/user/project/
```

Execute:

```bash
ssh user@mac_ip "cd project && python main.py"
```

Still runs on Mac hardware.

---

## Absolute Truth

If this works:

```bash
ssh user@mac_ip
```

Then you already control:

* Mac CPU
* Mac RAM
* Mac GPU
* Mac disk

Nothing else is required.

---

## Optional GPU Verification

Apple Silicon:

```bash
system_profiler SPDisplaysDataType
```

Metal test:

```bash
python - <<EOF
import torch
print(torch.backends.mps.is_available())
EOF
```

Intel + AMD/NVIDIA:

```bash
system_profiler SPDisplaysDataType
```

---

## Failure Modes

### VS Code stuck at "Installing Server"

```bash
rm -rf ~/.vscode-server
```

Reconnect.

---

### SSH Timeout

Ensure port 22 reachable:

```bash
nc -zv mac_ip 22
```

---

## Summary

* You never use Mac physically.
* SSH is the only requirement.
* VS Code Server executes everything.
* Linux is only a keyboard + screen.

End of system.
