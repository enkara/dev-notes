# Shell Commands 🔍

A small collection of useful shell commands (focused on `grep`, basic file inspection and disk usage).  
Examples are written for a typical Linux/WSL environment (paths may differ on pure Windows).

---

## 🔎 Find Files Containing Specific Text

### Basic Recursive Search
```
bash
# Search recursively for 'pattern' in all files under /path/to/somewhere
grep -rnw '/path/to/somewhere/' -e 'pattern'
```
Options used:

- `-r` / `-R` – recursive search in subdirectories  
- `-n` – show line numbers  
- `-w` – match whole words only  
- `-e` – pattern to search for  

---

## 🎯 Search with Include/Exclude Filters

You can limit the search to certain file types or folders using:

- `--include` – only search files that match this pattern  
- `--exclude` – skip files that match this pattern  
- `--exclude-dir` – skip directories that match this pattern  

### Examples
```
bash
# Search only in *.bat files under /c/Windows for a German error message
grep --include=\*.bat -rnw '/c/Windows/' -e "R konnte nicht gefunden werden."

# Search only in *.bat files under /c for the text "Temp"
grep --include=\*.bat -rnw '/c/' -e "Temp"
```
---

## 📁 Search in a Specific Directory Tree

### Example: Search in `.bat` Files Under `C:\Users` for `TEMP`
```
bash
# Search in *.bat files, recursively, starting from /c/Users
grep --include=\*.bat -rnw '/c/Users' -e "TEMP"
```
- Works well in **Git Bash** or **WSL**, where `C:\` is usually mounted as `/c/`.

---

## 🧰 Useful Grep Variants
```
bash
# Case‑insensitive search (e.g. "Error", "ERROR", "error")
grep -ri "error" /path/to/somewhere

# Only show file names that contain the pattern (no matching lines)
grep -rl "pattern" /path/to/somewhere

# Count how many matching lines per file
grep -rc "pattern" /path/to/somewhere

# Show context lines: 2 lines before and after each match
grep -rC 2 "pattern" /path/to/somewhere

# Exclude a specific directory (e.g., .git)
grep -rin --exclude-dir=.git "pattern" /path/to/somewhere
```
---

## 🔗 find + grep Combination

Sometimes `grep --include` / `--exclude` is not flexible enough.  
Then you can combine `find` and `grep`:
```
bash
# Find all *.log files and search for "ERROR"
find /var/log -name "*.log" -print0 | xargs -0 grep -n "ERROR"

# Find all *.cs files excluding obj and bin directories, then search for "TODO"
find . -name "*.cs" -not -path "./obj/*" -not -path "./bin/*" \
-print0 | xargs -0 grep -n "TODO"
```
---

## 📜 Quick File Inspection (Logs, Large Files)
```
bash
# Show the first 20 lines of a file
head -n 20 myfile.log

# Show the last 50 lines of a file
tail -n 50 myfile.log

# Follow log output in real time (like "live tail")
tail -f myfile.log

# View a file page by page (scroll with arrow keys, quit with 'q')
less myfile.log
```
Combination with `grep`:
```
bash
# See only lines with "ERROR" from a log and follow in real time
tail -f myfile.log | grep "ERROR"
```
---

## 💾 File Sizes & Disk Usage

### Human‑Readable Listings
```
bash
# List files with human‑readable sizes (KB, MB, GB)
ls -lh

# Include hidden files (starting with .)
ls -alh
```
### Directory Size (du)
```
bash
# Show total size of the current directory
du -sh .

# Show size of each subdirectory (one level)
du -sh */

# Show size of a specific directory
du -sh /var/log
```
Options used:

- `-s` – summarize, only total size  
- `-h` – human‑readable units  

### Free Disk Space (df)
```
bash
# Show disk usage for all mounted filesystems
df -h

# Show disk usage for a specific path (e.g. current filesystem)
df -h .
```
### Find Largest Files/Directories
```
bash
# Top 10 largest directories under current path
du -sh * | sort -h | tail -n 10

# Top 10 largest files under current path
find . -type f -printf "%s %p\n" | sort -n | tail -n 10
```
---
