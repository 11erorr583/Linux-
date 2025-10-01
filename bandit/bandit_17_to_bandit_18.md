# Bandit17 to Bandit18 - OverTheWire Writeup

## Problem Statement
In the home directory, there are two files:
- `passwords.old`
- `passwords.new`

The task is to find the password for Bandit18. The password is the only line in `passwords.new` that differs from `passwords.old`.

## Approach
We can use the `diff` command to compare the two files and identify the changed line.

### Commands Used
```bash
# List files in the home directory
ls

# Compare files side by side and show only differing lines
diff -y --suppress-common-lines passwords.old passwords.new
```

### Explanation
- `-y` : Displays the two files side by side, line by line.
- `--suppress-common-lines` : Hides lines that are identical in both files.

### Output Example
```bash
gvE89l3AhAhg3Mi9G2990zGnn42c8v20                              | x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```
- Left side: Password for Bandit17 (`gvE89l3AhAhg3Mi9G2990zGnn42c8v20`)
- Right side: Password for Bandit18 (`x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`)

## Result
- **Password for Bandit17:** gvE89l3AhAhg3Mi9G2990zGnn42c8v20
- **Password for Bandit18:** x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO

This approach quickly identifies the new password by showing only the changed line between the files.

