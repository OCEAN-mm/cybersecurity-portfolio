# OverTheWire Bandit Levels 10–15

## Level 10 → 11
- Goal: Decode Base64-encoded data.
- File: `data.txt`
- Command: `base64 -d data.txt`
- Learned:
  - `base64` encodes and decodes data.
  - `-d` means decode.
  - Base64 is commonly used to represent binary data as text.

## Level 11 → 12
- Goal: Decode text encrypted with ROT13.
- File: `data.txt`
- Command: `tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt`
- Learned:
  - `tr` translates characters.
  - ROT13 shifts each letter by 13 positions.
  - Input redirection `<` sends file contents to the command.

## Level 12 → 13
- Goal: Extract a password hidden inside multiple compressed and archived files.
- File: `data.txt` (hexdump)
- Steps:
  1. `mktemp -d` — Create a temporary directory.
  2. `cd /tmp/tmp.XXXXXX` — Enter the temporary directory.
  3. `cp ~/data.txt .` — Copy the hexdump file.
  4. `xxd -r data.txt > data` — Convert the hexdump back to binary.
  5. `file data` — Identify the file type.
  6. Rename the file according to its type (`.gz`, `.bz2`, `.tar`).
  7. Extract using:
     - `gunzip`
     - `bunzip2`
     - `tar xf`
  8. Repeat `file` and extraction until an ASCII text file is reached.
  9. `cat <final_file>` — Read the password.
- Learned:
  - `xxd -r` reverses a hexdump into binary data.
  - `file` identifies file formats.
  - `gunzip`, `bunzip2`, and `tar` extract compressed and archived files.
  - `mktemp -d` creates a safe temporary working directory.

## Level 13 → 14
- Goal: Use an SSH private key to log in as the next user.
- File: `sshkey.private`
- Commands:
  - `chmod 600 sshkey.private`
  - `ssh -i sshkey.private bandit14@localhost -p 2220`
- Command to read password:
  - `cat /etc/bandit_pass/bandit14`
- Learned:
  - `chmod 600` restricts file permissions to the owner.
  - `ssh -i` specifies a private key for authentication.
  - SSH keys are commonly used instead of passwords.

## Level 14 → 15
- Goal: Send the current password to a service listening on port 30000.
- Commands:
  - `nc localhost 30000`
  - Paste the current level password and press Enter.
- Learned:
  - `nc` (netcat) connects to TCP/UDP ports.
  - Some services return information after receiving valid input.
  - Netcat is one of the most useful tools in cybersecurity.

# Key Commands Learned
- `base64 -d`
- `tr`
- `mktemp -d`
- `xxd -r`
- `file`
- `gunzip`
- `bunzip2`
- `tar xf`
- `chmod 600`
- `ssh -i`
- `nc`

# Summary
These levels introduced encoding and decoding, character translation, archive extraction, SSH key authentication, and basic network communication. These are fundamental skills used in penetration testing, incident response, and automation.
