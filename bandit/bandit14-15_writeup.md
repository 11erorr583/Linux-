# Bandit Level 14 → Level 15 Writeup

## Objective
Retrieve the password for bandit15 by submitting the password for bandit14 to a service listening on port 30000 on localhost.

## Background
The challenge statement:
The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

Netcat (nc) is a simple tool for interacting with TCP/UDP ports. It's ideal here because we need to connect to a TCP port and send data.

## Solution (concise)
1. Connect to localhost on port 30000 using netcat.
2. Send the bandit14 password.
3. Read the response; the server replies with the bandit15 password.

## Commands and session
First, a common mistake that produces the error `nc: missing port number` is invoking nc without giving both host and port, e.g.
```
nc 127.0.0.1
```
Always provide a host and a port when connecting.

Working session:
```bash
# connect to localhost port 30000, verbose mode
nc -v localhost 30000
# after connection opens, type or paste the password for bandit14 (MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS) and press Enter
```

### Example session (input shown, output captured)
```
$ nc -v localhost 30000
Connection to localhost (127.0.0.1) 30000 port [tcp/*] succeeded!
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

## Explanation
Here's the thing, the service on port 30000 is a simple password checker. It reads what you send, compares it to the expected password for the previous level, and if correct, prints the next level's password.

We use netcat because:
- it's lightweight and built for raw TCP/UDP connections
- it connects interactively, so you can paste the password and immediately see the response
- ssh or telnet are not appropriate here: ssh requires an SSH server and authentication, telnet is similar but less common and not necessary

## Notes and troubleshooting
- If you see `nc: missing port number`, make sure you supplied both host and port, for example `nc -v localhost 30000`.
- If connection is refused, confirm the service is running and you are on the correct machine (bandit challenges usually run locally inside the game environment).
- Use `-v` for verbose output, it helps confirm connection status.

## Final
bandit14 password used
```
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
```

bandit15 password retrieved
```
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

Good luck on the next level.
