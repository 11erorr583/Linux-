# Bandit 29 --> Bandit 30

Find the password for bandit30 by cloning the git repository exposed on localhost and reading the README.

Goal

- Clone the repository in a temporary writable directory and inspect the appropriate branch to recover the password for the next level.

Exact method (no extra creativity)

1. Create a writable temporary directory and move into it

```bash
mktemp -d
# example output: /tmp/tmp.vGpMncTja8
cd /tmp/tmp.vGpMncTja8
```

2. Clone the remote repository using SSH on port 2220

```bash
GIT_SSH_COMMAND="ssh -p 2220" git clone ssh://bandit29-git@localhost/home/bandit29-git/repo
# when prompted for password, enter the current level password for bandit29:
# 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
```

Expected clone output

```
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

4. Read the current README.md (master branch shows a placeholder)

```bash
cat README.md
```
Output:
```
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: <no passwords in production!>
```

The password row is redacted with the message "no passwords in production!". In git-speak, "production" hints at branches, repositories often have multiple branches. The password may live on another branch.

5. List remote branches

```bash
git branch -r
```
Example output:
```
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```

We are currently on HEAD/master where the password is redacted. The password is likely on origin/dev or origin/sploits-dev, so switch to the dev branch and inspect it.

6. Create and switch to a local branch tracking origin/dev

```bash
git switch -c local_dev origin/dev
```
Confirm you're on it:
```bash
git checkout local_dev
# or: git switch local_dev
```

7. Inspect README.md on the dev branch

```bash
ls -la
cat README.md
```

Expected output on dev branch

```
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```

Password for bandit30

```
qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```

