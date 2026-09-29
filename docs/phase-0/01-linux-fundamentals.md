# Phase 0 — Topic 1: Linux Fundamentals

**Level:** Beginner · **Format:** Hands-on · **Assignment:** 0.1

## Learning objectives

By the end of this topic, you should be able to:
- Navigate a Linux filesystem and manage files/directories.
- Read, search, and follow log files.
- Explain Linux file permissions and modify them.
- Inspect processes, memory, disk usage, and system uptime.
- Create and execute a simple shell script.
- Explain the relationship between applications, shells, the kernel, and hardware.

## Why Linux matters for Kubernetes

Kubernetes worker nodes commonly run Linux. Containers share the host kernel, and many troubleshooting tasks involve inspecting processes, files, logs, networking, and permissions. Linux fluency makes it easier to understand what happens inside a container and on a Kubernetes node.

## A simplified Linux architecture

```text
Applications
     |
Shell / User tools
     |
Linux kernel
     |
Hardware
```

The shell accepts commands and launches programs. The kernel manages processes, memory, devices, filesystems, and networking.

## Essential commands

### Navigation

| Command | Purpose |
|---|---|
| `pwd` | Print the current directory |
| `ls` | List directory contents |
| `ls -l` | Long listing with permissions and ownership |
| `ls -la` | Include hidden files |
| `cd <path>` | Change directory |
| `cd ..` | Move to the parent directory |
| `cd ~` | Move to the home directory |
| `clear` | Clear the terminal |

### Files and directories

| Command | Purpose |
|---|---|
| `mkdir -p path` | Create directories, including parents |
| `touch file` | Create an empty file or update its timestamp |
| `cp source destination` | Copy a file |
| `mv source destination` | Move or rename a file |
| `rm file` | Delete a file |
| `rmdir directory` | Remove an empty directory |

**Caution:** `rm` can permanently delete data. Check the path before running destructive commands, and avoid using recursive deletion until you understand its effect.

### Reading and searching files

| Command | Purpose |
|---|---|
| `cat file` | Print a file |
| `less file` | Read a file interactively |
| `head file` | Show the first lines |
| `tail file` | Show the last lines |
| `tail -f file` | Follow new log entries |
| `grep "ERROR" file` | Search for matching lines |
| `find path -name "*.log"` | Find files by name |

### Permissions

Linux permissions apply to **owner**, **group**, and **others**. Each can have read (`r`), write (`w`), and execute (`x`) permissions.

- `755`: owner can read/write/execute; group and others can read/execute.
- `644`: owner can read/write; group and others can read.

Use `chmod` to change permissions:

```bash
chmod 755 script.sh
chmod 644 config.txt
```

### Processes and resources

| Command | Purpose |
|---|---|
| `ps aux` | List processes |
| `top` | Monitor processes interactively |
| `free -h` | Display memory usage (Linux) |
| `df -h` | Display filesystem capacity |
| `du -sh path` | Summarize directory size |
| `uptime` | Show uptime and load averages |
| `kill <pid>` | Send a signal to a process |

On macOS, use Activity Monitor or `vm_stat` for memory information; `free` is generally not installed by default.

## Hands-on lab

### 1. Create a workspace

```bash
mkdir -p ~/kubernetes-learning/linux-lab/{app,logs,scripts}
cd ~/kubernetes-learning/linux-lab
```

### 2. Create and inspect a log

```bash
cat > logs/app.log <<'EOF'
2026-01-01 INFO Application started
2026-01-01 INFO Listening on port 8080
2026-01-01 ERROR Database connection failed
2026-01-01 INFO Retrying connection
EOF

cat logs/app.log
grep "ERROR" logs/app.log
head -n 2 logs/app.log
tail -n 2 logs/app.log
```

### 3. Create a health-check script

Create `scripts/health-check.sh`:

```bash
#!/bin/bash

echo "Application health check"
echo "------------------------"
echo "Current directory: $(pwd)"
echo "Current user: $(whoami)"
echo "Current date: $(date)"
echo "Disk usage:"
df -h
```

Make it executable and run it:

```bash
chmod +x scripts/health-check.sh
./scripts/health-check.sh
```

### 4. Troubleshooting challenge

Remove the execute permission:

```bash
chmod 644 scripts/health-check.sh
./scripts/health-check.sh
```

Observe the permission error. Restore execution permission with `chmod +x scripts/health-check.sh` and run it again.

## Assignment 0.1 checklist

- [ ] Navigate directories using `pwd`, `ls`, and `cd`.
- [ ] Create, copy, rename, and delete test files.
- [ ] Create a log file and find an error using `grep`.
- [ ] Explain the difference between permissions `755` and `644`.
- [ ] Inspect processes and disk usage.
- [ ] Create and execute `health-check.sh`.
- [ ] Break and restore the script's execute permission.
- [ ] Explain the difference between a process, shell, and kernel.

## Knowledge check

1. Which command shows your current working directory?
2. What does `tail -f` do?
3. What does the execute bit allow?
4. Why should you be cautious with `rm -r`?
5. What role does the Linux kernel play?

**Completion gate:** Finish the checklist and be able to explain your command choices. Then continue to [Topic 2 — Networking Fundamentals](02-networking-fundamentals.md).
