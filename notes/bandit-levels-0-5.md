# OverTheWire Bandit Levels 0–5

## Level 0 → 1
- File: readme
- Command: `cat readme`
- Learned: Read file contents.

## Level 1 → 2
- File: `-`
- Command: `cat ./-`
- Learned: Use `./` when a filename starts with a dash.

## Level 2 → 3
- File: `--spaces in this filename--`
- Command: `cat ./--spaces\ in\ this\ filename--`
- Learned: Escape spaces with backslashes.

## Level 3 → 4
- Hidden file: `...Hiding-From-You`
- Commands:
  - `ls -la`
  - `cat ./...Hiding-From-You`
- Learned: Show hidden files with `ls -la`.

## Level 4 → 5
- Used `file ./*` to find the human-readable file.
- Command: `cat ./-file07`
- Learned: Identify file types using the `file` command.
