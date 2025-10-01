# Bandit Level 12 → Level 13

This is a concise, practical writeup template for the Bandit level where the password for the next level is stored in `data.txt`, a hexdump of a file that has been repeatedly compressed. Use this as your lab notes or to produce a clean writeup.

---

## Goal
Recover the password hidden inside `data.txt`. The file is a hexdump representing a binary that has been compressed multiple times. Your task, in order, is:
- create a private working directory under `/tmp`,
- copy the target `data.txt` in there,
- turn the hexdump back into binary, then
- identify and decompress each layer until you reach plain text containing the password.

## Safe working setup
```
# create a safe, temporary directory (hard-to-guess name)
workdir=$(mktemp -d /tmp/bandit12.XXXXXX)
cd "$workdir"

# copy the target file from the challenge directory to avoid editing the original
cp /path/to/data.txt ./data.txt
# e.g. cp /tmp/my_dir/data.txt ./data.txt  (adjust path that Bandit gave you)
```

Notes
- Use `mktemp -d` to avoid collisions and to keep your home clean.
- Work only inside this temporary folder.

## Turn the hexdump back into binary
Heed the exact format of the hexdump. Two common cases:

1) If `data.txt` is a plain hex stream (only hex digits and newlines):
```
xxd -r -p data.txt > layer0.bin
```

2) If `data.txt` is a full hexdump with offsets/ASCII columns, remove non-hex first:
```
sed 's/[^0-9A-Fa-f]//g' data.txt | xxd -r -p > layer0.bin
```

Verify the binary was created:
```
ls -l
file layer0.bin
hexdump -C layer0.bin | head
strings layer0.bin | head
```

## Identify and decompress layers
Use `file` to inspect `layer0.bin`. It will tell you what format the layer uses (gzip, bzip2, xz, POSIX tar, zip, or plain text).

Manual example (one layer):
```
file layer0.bin
# Suppose `file` says: gzip compressed data, was "data2.bin"
# Option A: let gzip pick the output name (stored in the gzip header)
mv layer0.bin layer0.gz
gunzip layer0.gz   # produces data2.bin (or layer0)

# Option B: don't rename, write decompressed output to a chosen file
gunzip -c layer0.bin > layer1.bin
# layer0.bin still exists
```

If `file` reports bzip2:
```
# rename to .bz2 or use bunzip2/gzip tools directly
mv layerN layerN.bz2
bunzip2 layerN.bz2
# or
bzip2 -d -k layerN.bz2   # keep original
```

If `file` reports xz:
```
mv layerN layerN.xz
xz -d layerN.xz
```

If `file` reports a tar archive (POSIX tar archive (GNU)):
```
# tar is an archive, extract it
tar -xf layerN
ls -l
# check the extracted members, one of them may be another compressed file or the password file
```

If `file` reports zip:
```
unzip layerN.zip -d unpacked
ls -l unpacked
```

If `file` says text or ASCII, view it:
```
file candidate
cat candidate
# the password is typically a short string you can copy
```

## Helpful automation loop (safe, read before running)
This loop will inspect the current file and try common decompressors until it reaches text or an archive. Review it before running.

```
f=layer0.bin
while true; do
  echo "-> $(file -b "$f")"
  case "$(file -b "$f")" in
    *gzip*) mv "$f" "$f.gz"; gunzip "$f.gz"; f="${f%.gz}";;
    *bzip2*) mv "$f" "$f.bz2"; bunzip2 "$f.bz2"; f="${f%.bz2}";;
    *XZ*|*xz*) mv "$f" "$f.xz"; xz -d "$f.xz"; f="${f%.xz}";;
    *Zip*) mv "$f" "$f.zip"; unzip -o "$f.zip" -d unpacked; ls unpacked; break;;
    *tar* ) tar -xf "$f"; ls; break;;
    *text*|*ASCII*) echo "Password file:"; cat "$f"; break;;
    *) echo "Unknown format, showing strings:"; strings "$f" | head -n 200; break;;
  esac
done
```

Notes about this loop
- The script renames files so common tools accept them. Renaming is safe because you are working in a disposable directory.
- If an archive extracts multiple files, check each extracted file. The password might be in one of them.
- If you ever see `file` report `data` or `unknown`, use `hexdump -C` and `strings` for clues.

## Common mistakes to avoid
- Do not use `>` incorrectly with `gzip -d`. `gzip -d file > out` does not decompress the way you expect. Use `gunzip -c file > out` to write decompressed output explicitly.
- `gzip` often expects `.gz` extension for some helper behavior; renaming to `.gz` before using `gunzip` is fine.
- Don’t run unknown executables in your main system. Use a disposable VM or `/tmp` folder.

## Example session (condensed)
```
mkdir -p /tmp/my_dir
cp /path/to/data.txt /tmp/my_dir/data.txt
cd /tmp/my_dir
xxd -r -p data.txt > layer0.bin
file layer0.bin
# -> gzip compressed data, was "data2.bin"
mv layer0.bin layer0.gz
gunzip layer0.gz
# -> produces data2.bin
file data2.bin
# continue until you reach a text file containing the password
cat password_file
```

## Final notes and verification
- Once you `cat` the final text and see the password, paste it into the Bandit prompt to move to level 13.
- If you get stuck, copy the first 30-60 lines of `data.txt` and paste them into your notes or share them here (non-sensitive), and someone can tell you the exact first command to run.

---

Good luck, and keep your workspace disposable. If you want, I can also generate a 1-page `.md` sample you can upload as a product, or produce a short screencast script showing each command.



## Final password

The password for level 13 is:

`FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

