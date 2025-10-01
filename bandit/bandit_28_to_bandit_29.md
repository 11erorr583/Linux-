# Bandit 28 --> Bandit 29

Find the password for bandit29 by cloning the git repository exposed on localhost and reading the README.

Goal

- Clone the repository in a temporary writable directory and inspect the git history to recover the password that was redacted in the current working tree.

Exact method (do not add or change anything)

1. Create a writable temporary directory and move into it

```bash
mktemp -d
# example output: /tmp/tmp.2Adg5g4Hqk
cd /tmp/tmp.2Adg5g4Hqk
```

2. Clone the remote repository using SSH on port 2220

```bash
GIT_SSH_COMMAND="ssh -p 2220" git clone ssh://bandit28-git@localhost/home/bandit28-git/repo
# when prompted for password, enter the current level password for bandit28:
# Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```

Expected clone output

```
bandit28-git@localhost's password:
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 9 (delta 2), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (9/9), done.
Resolving deltas: 100% (2/2), done.
```

3. List and change into the cloned repository

```bash
ls -la
cd repo
```

4. Read the current README.md (it will show the password redacted)

```bash
cat README.md
# output will show password: xxxxxxxxxx
```

5. Show the committed changes for README.md to reveal the removed line containing the real password

```bash
git show README.md
```

Expected `git show` output (the removed line contains the password)

```
commit 710c14a2e43cfd97041924403e00efb00b3a956e (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Fri Aug 15 13:16:10 2025 +0000

    fix info leak

diff --git a/README.md b/README.md
index d4e3b74..5c6457b 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials

 - username: bandit29
-- password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
+- password: xxxxxxxxxx
```

Password for bandit29

```
4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
```



