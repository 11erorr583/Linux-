# Bandit Level 18 -> 19, OverTheWire

**Objective**
Retrieve the password for bandit19. The password is stored in a file named `readme` in the home directory of the bandit18 account, but `.bashrc` has been modified to immediately log you out when you log in interactively with SSH.

---

## Environment
- Remote host: bandit.labs.overthewire.org
- Port: 2220
- User: bandit18
- Password for bandit18: `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

---

## Problem explained, short
When you SSH into bandit18 normally, `.bashrc` executes commands that end your session immediately. That prevents opening a regular interactive shell to read files. The solution is to avoid executing an interactive login shell, or force the remote shell to run a command directly on login, or request a different shell that doesn't source the modified `.bashrc`.

---

## Method 1 — Run a remote command with SSH (recommended, simplest)
### Idea
Use SSH to run a single remote command non-interactively. When SSH runs a command, it does not start an interactive login shell that executes the problematic `.bashrc` commands the same way as an interactive session, so the command can run and print the file contents before the session closes.

### Command
```
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
```

### Expected output (example)
After entering the password for bandit18, SSH will connect, execute `cat readme`, print the file contents to your local terminal and then disconnect. You should see ASCII art/banner lines followed by the password on a separate line. Example output:

```
This is an OverTheWire game server.
More information on http://www.overthewire.org/wargames

backend: gibson-1
bandit18@bandit.labs.overthewire.org's password:
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

The password `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8` is the password for bandit19.

### Why this works
SSH, when given a command, runs it directly on the remote side instead of spawning an interactive shell session that would source `.bashrc` in the same way. This avoids the forced logout.

---

## Method 2 — Force a different shell (alternate)
### Idea
Force SSH to allocate a pseudo-tty and run a simpler shell like `sh` which may not source the altered `.bashrc` in the same destructive way. This gives you an interactive shell on the remote side where you can then run `cat readme`.

### Command
```
ssh bandit18@bandit.labs.overthewire.org -p 2220 -t sh
```

After login you will get a prompt. Then run:
```
cat readme
```

The file contents will be printed, then you can exit.

### Notes
- `-t` forces allocation of a pseudo-tty. We need that for interactive shells.
- Some systems may still source files that log you out, but in this level `sh` works because it doesn’t execute the same `.bashrc` logic (bash-specific logout commands) or the server’s login configuration treats non-bash shells differently.

---

## Troubleshooting
- If the `cat readme` command output includes extra text (like banners), the password is still on a separate line. Look carefully for a line that looks like a long random string, that is the password.
- If you see immediate logout with both methods, try running `ssh ... 'ls -la'` or `ssh ... 'echo I_am_in'` to check whether executing any command works non-interactively.
- If the remote shell prints errors about login scripts, try `ssh -t ... /bin/bash --noprofile --norc` to start bash without reading initialization files, then `cat readme`. Example:

```
ssh bandit18@bandit.labs.overthewire.org -p 2220 -t /bin/bash --noprofile --norc
```

---


## Full example session (concise)
1. Run:
```
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
```
2. Enter the bandit18 password (`x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`) when prompted
3. Read the printed password, copy it, and use it to login to bandit19 (`cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`).

Alternate sequence using `sh`:
```
ssh bandit18@bandit.labs.overthewire.org -p 2220 -t sh
# then at remote prompt
cat readme
```

---

## Appendix: common commands used
- `ssh user@host -p PORT command` run command remotely without interactive shell
- `ssh user@host -p PORT -t sh` force a PTY and run sh
- `cat readme` print the file contents
- `man bash` read details about shell startup files

