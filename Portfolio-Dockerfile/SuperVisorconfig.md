# supervisord.conf` is a manifest of what processes must run inside the container

Think of it as:

```md
systemd for Docker
```

It tells Supervisor:

* What to start
* Where to start it
* How to restart it
* Where logs go

---

## File-level meaning

```md
[supervisord]
```

Global supervisor configuration.

```md
nodaemon=true
```

Run in foreground.
Docker requires this.

```md
logfile=/var/log/supervisor/supervisord.log
pidfile=/var/run/supervisord.pid
```

Where supervisor writes its own logs and PID.

---

## Concept

Each block:

```md
[program:SOMENAME]
```

= One running service.

Supervisor launches **all of them**.

---

## Example block (frontend-vite)

```md
[program:frontend-vite]
```

Name of service.

```md
command=pnpm --filter ms22-admin dev --host 0.0.0.0 --port 5173
```

Exact shell command Supervisor executes.

Equivalent to:

```md
cd directory
pnpm --filter ms22-admin dev --host 0.0.0.0 --port 5173
```

```md
directory=/app/MishraShardendu
```

Working directory before running command.

```md
autostart=true
```

Start when supervisor starts.

```md
autorestart=true
```

If crash → restart automatically.

```md
stderr_logfile=...
stdout_logfile=...
```

Where logs are written.

---

## Why `--filter ms22-admin`

You have pnpm monorepo.

This runs only that workspace:

```md
apps/MishraShardendu22-Frontend-AdminWebsite
```

Without filter, pnpm wouldn’t know which app.

---

## frontend-blog

Same idea:

```md
pnpm --filter ms22-blog dev --port 5174
```

Runs blog frontend.

---

## backend-personal

```md
command=/app/.../server
```

This is Go binary you built with:

```md
go build -o server
```

Supervisor executes compiled binary directly.

No Node involved.

---

## backend-blog

```md
command=pnpm start
directory=/app/MishraShardendu22-Backend-BlogWebsite
```

Runs:

```md
pnpm start → node dist/index.js
```

---

## nextjs-app

```md
command=pnpm dev
directory=/app/MS22
```

Runs Next.js dev server.

---

## Big Picture

When container starts:

```md
supervisord
 ├── pnpm dev (admin)
 ├── pnpm dev (blog)
 ├── pnpm start (blog backend)
 ├── server (go backend)
 └── pnpm dev (nextjs)
```

Docker sees:

```md
1 process → supervisord
```

Reality:

```md
5 servers running
```

---

## Why not shell script?

Shell scripts can’t restart crashed processes.

Supervisor can.

---

## What YOU control

Each service is just:

```md
command=
directory=
```

Everything else is management.

---

## Mental Model

`supervisord.conf` = **process wiring diagram**.

Nothing more.

---
