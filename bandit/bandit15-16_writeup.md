# Bandit Level 15 → Level 16 Writeup

## Level Goal
The password for the next level can be retrieved by submitting the password of the current level to port `30001` on `localhost` using **SSL/TLS encryption**.

---

## Key Idea
- `nc` (netcat) can send data over TCP, but it does **not** encrypt traffic.
- Since the problem specifically requires SSL/TLS, we must use a tool that supports encrypted connections.
- `openssl s_client` is designed exactly for this: it can connect to SSL/TLS services and let us interact with them.

---

## Steps Taken

### 1. Check the current password
We already have the password for **Bandit Level 15**:

```
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

### 2. Connect to port 30001 with TLS/SSL
Run the following command inside the Bandit15 shell:

```bash
openssl s_client -connect localhost:30001
```

Explanation of this command:
- `openssl` → cryptography toolkit
- `s_client` → client mode (connect to SSL/TLS services)
- `-connect localhost:30001` → connect to port 30001 on localhost with TLS/SSL

### 3. Observe the handshake
You will see certificate details and handshake output. This is expected, since the server uses a **self-signed certificate** named "SnakeOil". OpenSSL will warn about it, but we can ignore the warning because we trust the server in this challenge.

### 4. Send the current password
After the TLS connection is established, type the Level 15 password:

```
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

Press **Enter**.

### 5. Retrieve the new password
The server validates the password and responds with:

```
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

This is the password for **Bandit Level 16**.

---

## Final Answer
- Password for **Level 16**:  

```
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

---
# or alternative method:
- use: ncat command 
- parameter: --ssl it allows to connect to any port by enabling TLS/SSL encryption 
- `alternative command is: ` ncat --ssl localhost 30001
  ```
   Then enter password of current level and press enter 
   the password for next level will be prompted out
  ```

## Notes
- `nc` would fail here because it cannot handle SSL/TLS encryption.
- `openssl s_client` is the correct tool because it sets up an encrypted session before sending/receiving data.
- The self-signed certificate warnings (`SnakeOil`) are normal in practice servers like Bandit and can be ignored in this context.

