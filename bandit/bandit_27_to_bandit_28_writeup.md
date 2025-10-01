# Bandit 27 --> Bandit 28

Find the password for bandit28 by cloning the git repository exposed on localhost and reading the README.

What this level enforces

- You cannot clone into protected directories like /home, so use a temporary writable directory.

Quick steps

1. Create a temporary directory and move into it

```bash
mktemp -d
# note output, example: /tmp/tmp.EvzJ6DtQz9
cd /tmp/tmp.EvzJ6DtQz9
```

2. Clone the remote repository using the port 2220

- Git itself does not accept a -p flag. You must either embed the port in the ssh URL or tell git which ssh command to run.

```bash
# simplest form, port inside URL
git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo

# or, use GIT_SSH_COMMAND which also lets you pass identity file if needed
GIT_SSH_COMMAND="ssh -p 2220" git clone bandit27-git@localhost:/home/bandit27-git/repo
```

3. When prompted for the password for bandit27-git, use the same password you used to log in as bandit27

```
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
```

4. Inspect the cloned repository

```bash
ls
# you should see a directory named 'repo'
cd repo
ls
# README should be present
cat README
```

Result found in README

```
The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```
Complete writeup summary

1. create writable temp dir with mktemp -d
2. cd into that temp dir
3. run git clone using port 2220 (embed port or use GIT_SSH_COMMAND)
4. supply bandit27 password when prompted
5. cd repo and cat README to get bandit28 password

Password for bandit28

```
Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```
