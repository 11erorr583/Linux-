# Logging in to Bandit17 on OverTheWire

Here are the steps to successfully log in to Bandit17 using the provided private key.

## 1. Save the Private Key
Save the given RSA private key into a file named `bandit16.private`. Make sure you know its location, for example:
```
C:\bandit17\bandit16.private
```

## 2. Fix File Permissions (Windows)
SSH on Windows requires the private key to be **accessible only by your account**. Follow these steps:

1. Right-click the `bandit16.private` file → Properties → Security tab.
2. Click **Advanced**.
3. Remove all users/groups except your own account (e.g., remove `Authenticated Users`, `Everyone`, `SYSTEM`, etc.).
4. Ensure your account has **Full control**.
5. Apply changes.

> This step prevents the `UNPROTECTED PRIVATE KEY FILE` warning.

## 3. Login Using SSH
Open PowerShell or CMD and run the following command:

```
ssh -i C:\bandit17\bandit16.private bandit17@bandit.labs.overthewire.org -p 2220
```

- `-i` specifies the private key to use.
- `-p 2220` specifies the port number.

If permissions are correct, you should log in **without being prompted for a password**.

## Notes
- Always keep private keys secure. Only your account should have access.
- If SSH still asks for a password, double-check file permissions and ensure the key is **on a local drive** (not network/mapped drives).