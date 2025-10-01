# Bandit 30 → Bandit 31

Find the password for bandit31 by cloning the git repository exposed on localhost and inspecting tags and objects.

Goal

- Clone the repository in a temporary writable directory and locate the password which is stored as a git tag object.

Exact method (follow these exactly)

1. Create a writable temporary directory and move into it

```bash
mktemp -d
# example output: /tmp/tmp.Boijy4HrXC
cd /tmp/tmp.Boijy4HrXC
```

2. Clone the remote repository using SSH on port 2220

```bash
GIT_SSH_COMMAND="ssh -p 2220" git clone ssh://bandit30-git@localhost/home/bandit30-git/repo
```

Notes during clone

- On first connect you will be asked to confirm the host key. Answer yes.  
- You may see: Could not create directory '/home/bandit30/.ssh' (Permission denied). This is normal in the Bandit environment and the clone still completes.

3. Change into the cloned repository and inspect the working tree

```bash
ls -la
cd repo
cat README.md
```

Expected README.md output

```
just an epmty file... muahaha
```

4. Inspect git internals and tags

```bash
# list files including .git
ls -la .git
# look into packfiles if you want (they are binary): ls -la .git/objects/pack
# list tags in the repository
git tag -l
```

Example tag output

```
secret
```

5. Show the tag content to reveal the password

```bash
# tags point to objects; git show prints the object content
git show secret
```

Expected output (the password for bandit31)

```
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```

6. Use the revealed string as the password for bandit31

```bash
ssh bandit31@bandit.labs.overthewire.org -p 2220
# when prompted, paste: fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```

Notes and troubleshooting

- Do not attempt to `cat` pack files; they are binary. Use git commands (`git tag`, `git show`, `git rev-list`, `git cat-file`) to inspect repository objects.  
- If `git clone` fails with permission errors, ensure you are inside a writable `/tmp` directory created by `mktemp -d`.

Password for bandit31

```
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```

End of file

