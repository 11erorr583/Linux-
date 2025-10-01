# Bandit Level 16 to Level 17 Writeup

## Objective
Retrieve the password for **Bandit Level 17** by submitting the password of the current level (Bandit Level 16) to the correct port on localhost. The range of ports is **31000-32000**. Only one server will respond with the actual credentials; others will simply echo back what you send.

## Bandit16 Password
The password for Bandit16 is:
```
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

## Steps

### 1. Scan the Port Range
Use `nmap` to identify which ports are open in the range 31000-32000 on localhost.

```bash
bandit16@bandit:~$ nmap -p 31000-32000 localhost
```

**Sample Output:**
```
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown
```

### 2. Identify SSL/TLS Enabled Port
Check which of the open ports supports SSL/TLS. You can use either `ncat --ssl` or `openssl s_client`:

#### Using ncat
```bash
bandit16@bandit:~$ ncat --ssl localhost <port>
```
Test each port one by one. Only the port that supports SSL/TLS will not throw an `Input/output error`. In this case, **31790** is SSL/TLS enabled.

#### Using openssl
```bash
openssl s_client -connect localhost:31790
```

### 3. Submit the Password
Once the SSL-enabled port is identified, send the password of Bandit16. This can be done by entering it after connecting via `ncat --ssl`:

```bash
bandit16@bandit:~$ ncat --ssl localhost 31790
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

### 4. Retrieve the RSA Private Key
The server responds with the password for Bandit Level 17 as an **RSA private key**. Save it to a file:

```bash
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```

Save this key as:
```bash
bandit16.private
```

### 5. Use the Key to Login to Bandit17
SSH into Bandit17 using the RSA private key:

```bash
ssh -i bandit16.private bandit17@bandit.labs.overthewire.org -p 2220
```

## Summary
- **Step 1:** Scan ports using `nmap`.
- **Step 2:** Identify SSL/TLS enabled port using `ncat --ssl` or `openssl s_client`.
- **Step 3:** Submit Bandit16 password.
- **Step 4:** Retrieve RSA private key.
- **Step 5:** SSH into Bandit17 using the private key.

This completes the Bandit Level 16 to Level 17 challenge.

