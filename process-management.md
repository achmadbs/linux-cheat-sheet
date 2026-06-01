# Linux Process Management Cheat Sheet

Quick reference for inspecting, filtering, stopping, and managing Linux processes.

## Process Basics

A process is a running program. Each process has a process ID, usually called a PID.

Common process fields:

| Field | Meaning |
| --- | --- |
| `PID` | Process ID |
| `PPID` | Parent process ID |
| `USER` | User that owns the process |
| `%CPU` | CPU usage |
| `%MEM` | Memory usage |
| `STAT` | Process state |
| `COMMAND` | Command that started the process |

## List Processes

Show processes for the current terminal:

```bash
ps
```

Show all processes:

```bash
ps aux
```

Show all processes in full-format output:

```bash
ps -ef
```

Show a process tree:

```bash
ps auxf
```

If available, `pstree` gives a clearer tree:

```bash
pstree -p
```

## Find Processes

Find processes by text:

```bash
ps aux | grep nginx
```

Find a PID by process name:

```bash
pgrep nginx
```

Show matching processes with names:

```bash
pgrep -a nginx
```

Find processes owned by a user:

```bash
pgrep -u achmad -a
```

## Monitor Processes

Interactive process monitor:

```bash
top
```

More user-friendly process monitor, if installed:

```bash
htop
```

Refresh process list every two seconds:

```bash
watch -n 2 'ps aux --sort=-%cpu | head'
```

Show highest CPU usage:

```bash
ps aux --sort=-%cpu | head
```

Show highest memory usage:

```bash
ps aux --sort=-%mem | head
```

## Kill Processes

Stop a process by PID:

```bash
kill 1234
```

Force kill a process by PID:

```bash
kill -9 1234
```

Stop processes by name:

```bash
pkill nginx
```

Force kill processes by name:

```bash
pkill -9 nginx
```

Kill all processes matching a command pattern:

```bash
pkill -f "node server.js"
```

## Common Signals

Signals tell a process what action to take.

| Signal | Number | Meaning | Common use |
| --- | --- | --- | --- |
| `SIGTERM` | `15` | Ask process to stop gracefully | Default `kill` behavior |
| `SIGKILL` | `9` | Force process to stop immediately | Last resort |
| `SIGHUP` | `1` | Hang up or reload config | Reload daemons |
| `SIGINT` | `2` | Interrupt process | Same idea as `Ctrl+C` |
| `SIGSTOP` | `19` | Pause process | Suspend without exiting |
| `SIGCONT` | `18` | Continue paused process | Resume after stop |

Prefer `SIGTERM` first:

```bash
kill -15 1234
```

Use `SIGKILL` only when the process refuses to stop:

```bash
kill -9 1234
```

## Background Jobs

Run a command in the background:

```bash
long-command &
```

List shell jobs:

```bash
jobs
```

Move the latest background job to the foreground:

```bash
fg
```

Move a specific job to the foreground:

```bash
fg %1
```

Resume a stopped job in the background:

```bash
bg %1
```

Stop the foreground process without killing it:

```text
Ctrl+Z
```

Interrupt the foreground process:

```text
Ctrl+C
```

## Keep Processes Running After Logout

Run a command immune to hangups:

```bash
nohup long-command &
```

Start or attach to a terminal multiplexer:

```bash
tmux
```

Detach from `tmux`:

```text
Ctrl+B, then D
```

List `tmux` sessions:

```bash
tmux ls
```

Attach to a `tmux` session:

```bash
tmux attach -t 0
```

## Process Priority

Linux uses niceness to influence CPU scheduling priority.

| Nice value | Priority |
| --- | --- |
| Lower value | Higher priority |
| Higher value | Lower priority |
| `0` | Default |
| `19` | Lowest common priority |
| `-20` | Highest common priority |

Start a command with lower priority:

```bash
nice -n 10 long-command
```

Change priority of a running process:

```bash
renice 10 -p 1234
```

Increase priority, usually requires root:

```bash
sudo renice -5 -p 1234
```

## Useful Recipes

Find and stop a process by port:

```bash
sudo lsof -i :3000
kill 1234
```

Force stop a process using a port:

```bash
sudo fuser -k 3000/tcp
```

Show a specific PID:

```bash
ps -p 1234 -f
```

Show child processes of a PID:

```bash
pgrep -P 1234 -a
```

Watch a single process:

```bash
watch -n 1 'ps -p 1234 -o pid,ppid,user,%cpu,%mem,stat,command'
```

## Quick Reference

| Command | Use |
| --- | --- |
| `ps aux` | List all processes |
| `ps -ef` | List all processes in full format |
| `pgrep -a name` | Find processes by name |
| `top` | Monitor processes interactively |
| `htop` | Friendlier process monitor, if installed |
| `kill PID` | Gracefully stop a process |
| `kill -9 PID` | Force kill a process |
| `pkill name` | Stop processes by name |
| `jobs` | List shell jobs |
| `fg %1` | Bring job to foreground |
| `bg %1` | Resume job in background |
| `nohup command &` | Keep command running after logout |
| `nice -n 10 command` | Start command with lower priority |
| `renice 10 -p PID` | Change running process priority |
