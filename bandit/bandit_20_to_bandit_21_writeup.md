# Bandit Level 20 → 21 — Writeup

## Goal
Get the password for bandit21 by using the setuid binary in the home directory. The binary connects to localhost on a port you choose, reads one line, checks it against the bandit20 password, and if it matches, returns the bandit21 password over the same connection.

## Quick summary
Run a listener on the Bandit host, let the setuid binary connect to it, send the bandit20 password, then read the returned bandit21 password.

## What you need
- A shell on the Bandit host as bandit20.  
- `nc` (netcat) available on the host. If `nc -l PORT` fails, try `nc -l -p PORT` or `ncat -l PORT`.  
- The bandit20 password: `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO` (you should already have this).

---

## Step-by-step (interactive, two-screen method)
This is the simplest, most reliable approach using `screen` or `tmux` so you stay in one SSH connection.

### 1) Open a screen session (single terminal) and make two windows
```
screen -S bandit
# inside screen: Ctrl-a c   -> creates window 1 (listener)
# Ctrl-a c again -> creates window 2 (client)
# switch windows with Ctrl-a n or Ctrl-a p
```

### 2) In the first window, start the listener on an unused port (choose >1024)
```
# common netcat variants, pick the one that works
nc -l 12345
# or
nc -l -p 12345
# or
ncat -l 12345
```
Leave this window open and wait for the connection.

### 3) Switch to the second window and run the setuid client with the same port
```
./suconnect 12345
# or whatever the setuid binary is named in the directory, e.g.:
./bandit21-do 12345
```
The binary will connect to `localhost:12345` on the remote host and wait for a line of input.

### 4) Back in the first window (the listener), type the bandit20 password and press Enter
```
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```
You should see a confirmation message in the client window like `Read: <password>` and `Password matches, sending next password`.

### 5) The listener will receive the bandit21 password. It will look like this:
```
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
```
This is the password for bandit21.

---

## Why this works
- The setuid binary runs with the owner’s privileges (the owner can read `/etc/bandit_pass/bandit20`). It connects to a local TCP port you control and reads a line. If that line equals the bandit20 password, the program sends the bandit21 password back on the same socket. By running a listener and sending the known password, you receive the next password.

## Final result
Password for bandit21 (captured during the exchange):

```
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
```

Use it to log in as bandit21.
