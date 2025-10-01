# Bandit 21 -> Bandit 22 writeup

Goal, find the password for level 22

## Quick summary

There is a cron job running as bandit22 that periodically writes the level 22 password to a world-readable file in /tmp. We inspect the cron configuration, find the script executed, and read the temporary file after cron creates it.

## Steps and commands

1. List cron jobs in /etc/cron.d

```bash
cd /etc/cron.d/
ls -l
```

Example output

```text
bandit21@bandit:/etc/cron.d$ ls -l
total 40
-r--r----- 1 root root  47 Aug 15 13:16 behemoth4_cleanup
-rw-r--r-- 1 root root 123 Aug 15 13:09 clean_tmp
-rw-r--r-- 1 root root 120 Aug 15 13:16 cronjob_bandit22
-rw-r--r-- 1 root root 122 Aug 15 13:16 cronjob_bandit23
-rw-r--r-- 1 root root 120 Aug 15 13:16 cronjob_bandit24
-rw-r--r-- 1 root root 201 Apr  8  2024 e2scrub_all
-r--r----- 1 root root  48 Aug 15 13:17 leviathan5_cleanup
-rw------- 1 root root 138 Aug 15 13:17 manpage3_resetpw_job
-rwx------ 1 root root  52 Aug 15 13:19 otw-tmp-dir
-rw-r--r-- 1 root root 396 Jan  9  2024 sysstat
```

2. Read the cron file for bandit22

```bash
cat cronjob_bandit22
```

Output

```text
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

What this means, cron runs /usr/bin/cronjob_bandit22.sh on reboot and every minute as user bandit22.

3. Inspect the script executed by cron

```bash
cd /
cat /usr/bin/cronjob_bandit22.sh
```

Output

```bash
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

Explanation, the script makes a file in /tmp with predictable name and sets its permissions to 644, then writes the contents of /etc/bandit_pass/bandit22 into that file. The file becomes readable by the current user and others.

4. Read the temporary file to get the password

Because cron runs the script every minute, the file exists and is world-readable. Read it directly

```bash
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

Password found in the file

```text
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

## Why this works

The cron job runs as bandit22 and dumps the secret password into a fixed file in /tmp with permissive mode 644. /tmp is world-readable, so any user on the box can read the file while it exists. Cron runs every minute so the file is regularly recreated.

## Final answer

Password for bandit22

```
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

