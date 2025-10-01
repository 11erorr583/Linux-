# Bandit 22 -> Bandit 23

```
A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.
```

Goal, find the password for level 23

## Summary
A cron job runs a script every minute that writes the level 23 password to a file in /tmp. The filename is the MD5 hash of the exact text `I am user bandit23`. Reproduce that hash and read the file to get the password.

## Steps

1. Check cron configuration

```bash
cd /etc/cron.d
ls -l
cat cronjob_bandit23
```

Output shows the cron job runs `/usr/bin/cronjob_bandit23.sh` as bandit23 every minute.

2. Read the script

```bash
cd /
cat /usr/bin/cronjob_bandit23.sh
```

Script contents

```bash
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

3. What it does, simply

- The script runs as user bandit23 when invoked by cron.
- It computes the MD5 of the string `I am user bandit23` and uses that as a filename in /tmp.
- It copies `/etc/bandit_pass/bandit23` into `/tmp/<hash>`.

4. Recreate the filename and read the password

```bash
# find filename (hash)
echo I am user bandit23 | md5sum | cut -d' ' -f1

# read the password file
cat /tmp/$(echo I am user bandit23 | md5sum | cut -d' ' -f1)
```

Example (what I ran)

```bash
$ echo I am user bandit23 | md5sum | cut -d' ' -f1
8ca319486bfbbc3663ea0fbe81326349

$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```

## Answer
Password for bandit23

```
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```

