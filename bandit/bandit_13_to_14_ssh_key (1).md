# Bandit 13 → Bandit 14 Walkthrough

## Level Overview

**Bandit 13 Objective:**
The password for the next level (`bandit14`) is stored in `/etc/bandit_pass/bandit14` and **can only be read by the user `bandit14`**.  

Unlike previous levels, **Bandit 13 does not give the password directly**. Instead, you are provided with a **private SSH key** that allows you to log into `bandit14`.  

> Note: `localhost` refers to the same machine you are connected to via SSH.

---

## Step 1: Connect to Bandit 13

1. Open your terminal.
2. Connect using SSH to Bandit 13:

```bash
ssh bandit13@bandit.labs.overthewire.org -p 2220
```

3. Enter the password for Bandit 13:  

```
FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

4. Verify connection:

```bash
whoami
```

Expected output:

```
bandit13
```

---

## Step 2: Inspect the files

Check what’s in your home directory:

```bash
ls -l
```

You should see a file named:

```
sshkey.private
```

This is the **private key** you will use to log into Bandit 14.

---

## Step 3: Log in to Bandit 14 using the private key

Run the following SSH command **from Bandit 13**:

```bash
ssh -i sshkey.private bandit14@localhost -p 2220
```

Explanation of the options:

- `-i sshkey.private` → specifies the private key file to use for login.  
- `bandit14@localhost` → username is `bandit14`, host is `localhost` (same machine).  
- `-p 2220` → port to connect, same as Bandit server port.  

> If the key had a passphrase, SSH would prompt you to enter it. Bandit keys usually do not have a passphrase.

Once the command succeeds, you will be **logged in as bandit14**.

---

## Step 4: Retrieve the next level password

The password for Bandit 15 is stored in:

```
/etc/bandit_pass/bandit14
```

Since only `bandit14` can read it, you need to open this file **after logging in as bandit14**:

```bash
cat /etc/bandit_pass/bandit14
```

Example output:

```
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
bandit14@bandit:~$
```

- This reveals the password for Bandit 15.  
- Copy this password to use for the next level login.

---

## Problem Explanation

- The **issue** is that the password file is **restricted to bandit14**, so the current user (bandit13) cannot read it.  
- Normal `cat /etc/bandit_pass/bandit14` as bandit13 will **fail due to permissions**.  

**Solution:**  
- Bandit 13 provides a **private SSH key**.  
- Using key-based SSH login allows you to **log in as bandit14**, bypassing the need for the password.  
- Once logged in as bandit14, you have the **required permission** to read the password file.

---

## Summary

1. Connect to Bandit 13.  
2. Locate the private key (`sshkey.private`).  
3. SSH into Bandit 14 using the private key.  
4. Read the password for Bandit 15.  

This demonstrates **key-based authentication**, a common method for secure login without sharing passwords.

