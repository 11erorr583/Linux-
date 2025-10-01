# Bandit 24 -> Bandit 25 — Your method

Follow these exact steps you described to brute-force the 4-digit pin using a single connection.

## Steps

1. Create a temporary directory

```bash
mktemp -d
# example output: /tmp/tmp.jlvXHJKQhd
```

2. Change into that directory

```bash
cd /tmp/tmp.jlvXHJKQhd
```

3. Open the editor and create the script

```bash
nano my_script.sh
```

4. In the editor paste exactly this script

```bash
#!/bin/bash
for i in {0000..9999}
do
 printf '%s %s\n' "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8" "$i" >> possibilites.txt
done
```

Save and exit (Ctrl+X, Y, Enter)
### Explanation of my_script.sh

What this script does, line by line:

- Shebang `#!/bin/bash`
  tells the system to run the script with bash.

- `for i in {0000..9999}`
  this is a bash brace expansion. It produces the sequence 0000 0001 0002 ... 9999, each as a four-digit string. That yields all 10,000 pin candidates.

- `do` ... `done`
  standard bash loop construct. The body runs once per pin.

- `printf '%s %s\n' "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8" "$i" >> possibilites.txt`
  formats one line per attempt using printf. `%s %s\n
` means: first string, a space, second string, newline. The first string is the bandit24 password, the second is the current pin candidate held in variable i. The output is appended to possibilites.txt because of `>>`. If the file doesn't exist, `>>` creates it. Running the loop produces 10,000 lines of the form

  gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0000
  gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0001
  ...
  gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 9999

Why use printf and not echo

- printf is predictable and consistent with formatting. echo can behave inconsistently with escape sequences or flags across shells. printf with an explicit format eliminates surprises.

Why append with >> instead of overwrite

- Using `>>` inside the loop adds each new line to the file. If you used `>` inside the loop, each iteration would overwrite the file, leaving only the final line. Using `>>` ensures all attempts are stored.


5. Run the script

```bash
bash my_script.sh
```

6. Verify files in the directory

```bash
ls
# you should see:
# my_script.sh  possibilites.txt
```

7. Inspect the generated possibilities (optional)

```bash
cat possibilites.txt
# sample lines should look like:
# gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 2072
# gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 2073
# ...
```

8. Send all possibilities through the daemon in one connection

```bash
cat possibilites.txt | nc localhost 30002
```
## Explanation of `cat possibilites.txt | nc localhost 30002`

Breakdown:

- `cat possibilites.txt`
  reads the entire file and writes its contents to stdout (standard output). Each line contains a password plus a pin separated by a space.

- `|` (pipe)
  connects the stdout of `cat` to the stdin of the next command. That means each line produced by cat is fed into nc as if typed by a user.

- `nc localhost 30002`
  starts netcat, which opens a TCP connection to localhost on port 30002. Netcat reads from its stdin and sends those bytes over the network to the daemon, and it writes any response from the daemon to stdout so you can see it.

What happens when you run the whole pipeline

- The pipe feeds each of the 10,000 lines to nc in sequence, without closing and reopening the connection. Netcat sends each line to the daemon, the daemon processes that single attempt and sends back a response. Because the pipe keeps the connection open, the daemon receives each attempt in the same session and replies accordingly. When it finds the correct pin, it responds with Correct! and the password for bandit25.


9. Expected behavior

The daemon will print Wrong! for incorrect attempts. When it finds the right pin it will print Correct! followed by the bandit25 password. Example final lines:

```
Correct!
The password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
```

Why this satisfies the challenge requirement

- The problem statement forbids creating a new connection for each try. Using `cat | nc` opens exactly one TCP connection for the entire stream of 10,000 attempts, satisfying the constraint.

Caveats and behavior to watch for

- Buffering: When you write a lot into a pipe, there can be buffering between the writer and nc. For this challenge nc reads line-based input and the daemon replies per attempt; cat will stream lines as fast as possible into nc. The daemon will still process and reply. If you want tighter control over timing or immediate reaction, use the coprocess script that writes a line then reads the daemon response before continuing.

- Visibility: `cat | nc` will show the daemon responses on your terminal. When the daemon prints Correct! you will see it and the password that follows.

