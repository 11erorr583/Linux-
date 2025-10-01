# Bandit Level 26 --> Level 27 - Writeup

Goal

Get the password for bandit27.

Prerequisites

- You are logged in as bandit26 on the Bandit server.
- The file bandit27-do is present in your working directory and is executable.

Method (exact steps you used)

1. Inspect the directory and find the executable

```
ls -la
```
You should see bandit27-do with setuid bit set and owned by bandit27.

2. Check file type

```
file bandit27-do
```
Expect a 32-bit setuid ELF executable.

3. Run the program without arguments to see usage

```
./bandit27-do
```
It prints: Run a command as another user. Example: ./bandit27-do id

4. Verify it runs commands with bandit27 privileges

```
./bandit27-do id
```
Expected output example:

```
uid=11026(bandit26) gid=11026(bandit26) euid=11027(bandit27) groups=11026(bandit26)
```
This shows the effective UID is bandit27, meaning commands executed by the program run as bandit27.

5. Use the program to read the password file as bandit27

```
./bandit27-do cat /etc/bandit_pass/bandit27
```
This prints the password for bandit27.

Result

The password printed was:

```
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
```

Why this works, briefly

- bandit27-do is setuid to bandit27, so when executed its effective UID is bandit27
- the program accepts a command and runs it with that effective UID
- running cat on the protected password file therefore succeeds and prints the password

Notes and cautions

- Never run arbitrary setuid programs you don't trust with unvalidated input on real systems
- This is a deliberate learning exercise in privilege escalation for the Bandit wargame

End


