# Bandit Level 31 --> Writeup

## Problem
There is a git repository at `ssh://bandit31-git@localhost/home/bandit31-git/repo` on port 2220. The password for `bandit31-git` is the same as `bandit31`.

Task: push a file named `key.txt` with content `May I come in?` to branch `master` to get the next level password.

## Steps

```sh
# create temporary directory
mktemp -d
cd /tmp/tmp.XXXXXX

# clone repo using correct SSH port
GIT_SSH_COMMAND="ssh -p 2220" git clone ssh://bandit31-git@localhost/home/bandit31-git/repo
cd repo

# create and switch to a branch tracking origin/master
git switch -c local_master origin/master

# create key.txt with required content
printf "May I come in?" > key.txt

# force add due to .gitignore
git add -f key.txt

# commit change
git commit -m "Add key.txt"

# push to remote using SSH port 2220
GIT_SSH_COMMAND="ssh -p 2220" git push origin master
```
output:
after entering the password for bandit 31 when asked
output is:
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 2 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 325 bytes | 325.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
remote: ### Attempting to validate files... ####
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
remote: Well done! Here is the password for the next level:
remote: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
To ssh://localhost/home/bandit31-git/repo
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to 'ssh://localhost/home/bandit31-git/repo'
bandit31@bandit:/tmp/tmp.M45Bwa5cK6/repo$


## Notes
- `.gitignore` ignores `*.txt`, so `git add -f` is necessary.
- Use `GIT_SSH_COMMAND="ssh -p 2220"` to push over the correct port.
- If prompted about host authenticity, type `yes` and then provide the password for `bandit31-git`.
- After a successful push, the server connects to the bandit 31.

## Next level password
3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K


