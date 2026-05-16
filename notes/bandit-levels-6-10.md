# OverTheWire Bandit Levels 6–10

## Level 5 → 6
- Goal: Find a file that is:
  - Human-readable
  - 1033 bytes in size
  - Not executable
- Command: `find . -size 1033c ! -executable`
- File: `.file2`
- Command to read: `cat ./.file2`
- Learned:
  - `find` searches for files and directories.
  - `-size 1033c` means exactly 1033 bytes.
  - `! -executable` excludes executable files.

## Level 6 → 7
- Goal: Find a file that is:
  - Owned by user `bandit7`
  - Owned by group `bandit6`
  - 33 bytes in size
- Command: `find / -user bandit7 -group bandit6 -size 33c 2>/dev/null`
- File: `/var/lib/dpkg/info/bandit7.password`
- Command to read: `cat /var/lib/dpkg/info/bandit7.password`
- Learned:
  - `/` searches the entire filesystem.
  - `-user` filters by file owner.
  - `-group` filters by file group.
  - `-size 33c` means exactly 33 bytes.
  - `2>/dev/null` hides permission denied errors.

## Level 7 → 8
- Goal: Find the line containing the word `millionth`.
- File: `data.txt`
- Command: `grep millionth data.txt`
- Learned:
  - `grep` searches for patterns inside files.
  - It prints lines that match the search term.

## Level 8 → 9
- Goal: Find the only line that appears exactly once.
- File: `data.txt`
- Command: `sort data.txt | uniq -u`
- Learned:
  - `sort` arranges lines alphabetically.
  - `uniq -u` prints lines that appear only once.
  - The pipe `|` sends the output of one command to another.

## Level 9 → 10
- Goal: Find the password hidden in a binary file after several `=` characters.
- File: `data.txt`
- Command: `strings data.txt | grep "==="`
- Learned:
  - `strings` extracts readable text from binary files.
  - `grep "==="` filters lines containing three equals signs.

# Key Commands Learned
- `find`
- `grep`
- `sort`
- `uniq`
- `strings`
- `2>/dev/null`
- `|` (pipe)

# Summary
These levels introduced powerful Linux commands for searching files, filtering text, and extracting useful information from binary data. These tools are used extensively in cybersecurity, penetration testing, and digital forensics.
