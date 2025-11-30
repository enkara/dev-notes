# PowerShell Commands ⚡

A small collection of useful PowerShell commands and one‑liners for everyday development.

---

## 📚 References

- [PowerShell Documentation](https://learn.microsoft.com/powershell/)
- [PowerShell Gallery](https://www.powershellgallery.com/)

---

## 🧠 Basics
``` 
powershell

# Show PowerShell version
$PSVersionTable

# List available commands (cmdlets, functions, aliases)
Get-Command

# Get help for a command
Get-Help Get-Process
Get-Help Get-Process -Online

# Update help (once in a while)
Update-Help
```
---

## 📁 Files & Directories
```
powershell
# List files in current directory
Get-ChildItem

# List files (like 'ls -la')
Get-ChildItem -Force

# List only .log files, recursive
Get-ChildItem -Recurse -Filter *.log

# Change directory
Set-Location C:\Projects\MyApp       # cd C:\Projects\MyApp

# Create directory
New-Item -ItemType Directory -Path .\logs

# Delete file
Remove-Item .\old.log

# Delete directory recursively
Remove-Item .\temp -Recurse -Force
```
---

## 🔎 Search Text in Files (grep‑like)

### Simple Search
```
powershell
# Search for 'ERROR' in all .log files (current directory)
Select-String -Path *.log -Pattern "ERROR"

# Search recursively in all .cs files
Select-String -Path .\*.cs -Pattern "TODO" -Recurse
```
### Show Only File Names
```
powershell
# Show only files that contain the pattern
Select-String -Path .\*.cs -Pattern "MyClass" -Recurse |
Select-Object -ExpandProperty Path -Unique
```
### Case‑Insensitive / Regex
```
powershell
# Case‑insensitive search
Select-String -Path .\*.log -Pattern "error" -Recurse -CaseSensitive:$false

# Regex search (lines starting with ERROR)
Select-String -Path .\*.log -Pattern "^ERROR" -Recurse
```
---

## 📜 Logs & Output
```
powershell
# Show last 50 lines of a log file (like tail -n 50)
Get-Content .\app.log -Tail 50

# Follow log file (like tail -f)
Get-Content .\app.log -Wait

# Pipe and filter log lines containing 'ERROR'
Get-Content .\app.log -Wait | Select-String "ERROR"
```
---

## 🧰 Processes & Ports
```
powershell
# List processes
Get-Process

# Filter by name (e.g. java)
Get-Process java

# Stop a process by Id
Stop-Process -Id 12345 -Force

# Stop all java processes (be careful!)
Get-Process java | Stop-Process -Force
```
### Find Which Process Uses a Port
```
powershell
# Via netstat
netstat -ano | Select-String ":8080"

# On newer Windows with Get-NetTCPConnection
Get-NetTCPConnection -LocalPort 8080 |
Select-Object LocalAddress,LocalPort,State,OwningProcess
```
---

## 🔗 Web & APIs (Quick Checks)
```
powershell
# Simple HTTP GET
Invoke-WebRequest https://example.com

# GET and show only status code
(Invoke-WebRequest https://example.com).StatusCode

# Call a JSON API and get object (deserialized)
$response = Invoke-RestMethod https://api.github.com/repos/PowerShell/PowerShell
$response.full_name
$response.stargazers_count
```
---

## 🧾 JSON Handling
```
powershell
# Read a JSON file
$json = Get-Content .\config.json | ConvertFrom-Json

# Access properties
$json.ConnectionString
$json.Logging.Level

# Modify and write back to file
$json.Logging.Level = "Debug"
$json | ConvertTo-Json -Depth 10 | Set-Content .\config.json
```
---

## ⚙️ Execution Policy & Scripts
```
powershell
# Show current execution policy
Get-ExecutionPolicy

# Allow local scripts for current user (common dev setup)
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```
Run a script:
```
powershell
# From current directory (note the .\)
.\build.ps1
```
---

## 👤 Profile Script (Auto‑Loaded Commands)

You can store aliases and helper functions in your PowerShell profile  
(this script runs automatically when PowerShell starts):
```
powershell
# Show profile path
$PROFILE

# Create profile file if it does not exist
if (!(Test-Path $PROFILE)) {
New-Item -ItemType File -Path $PROFILE -Force
}
```
Example content of your profile (`$PROFILE`):
```
powershell
# Alias for git status
Set-Alias gs "git status"

# Quick cd into your dev folder
function cdev { Set-Location "C:\Dev" }

# Tail current directory log
function tail-log { Get-Content .\app.log -Tail 100 -Wait }
```
---

## 🧪 Quick One‑Liners for Devs
```
powershell
# Count lines of code in all .cs files (rough)
Get-ChildItem -Recurse -Filter *.cs |
Get-Content |
Measure-Object -Line

# Check if a TCP port is open (localhost:8080)
Test-NetConnection -ComputerName localhost -Port 8080

# Zip a folder
Compress-Archive -Path .\build\* -DestinationPath .\build.zip

# Unzip
Expand-Archive .\build.zip -DestinationPath .\unpacked
```
---

## 🔀 PowerShell vs. Bash – Command Mapping

Common mappings when switching between **Bash (Linux/WSL/Git Bash)** and **PowerShell**.

### Navigation & Files

| Task                            | Bash                         | PowerShell                               |
|---------------------------------|------------------------------|------------------------------------------|
| Current directory               | `pwd`                        | `Get-Location` or `pwd` (alias)         |
| List files                      | `ls`                         | `Get-ChildItem` or `ls` / `dir`         |
| List all files (incl. hidden)   | `ls -la`                     | `Get-ChildItem -Force`                  |
| Change directory                | `cd /path`                   | `Set-Location C:\path` or `cd C:\path`  |
| Make directory                  | `mkdir logs`                 | `New-Item -ItemType Directory logs`     |
| Remove file                     | `rm file.txt`                | `Remove-Item file.txt`                  |
| Remove dir recursively          | `rm -rf dir`                 | `Remove-Item dir -Recurse -Force`       |

### Text / Logs

| Task                                   | Bash                                    | PowerShell                                      |
|----------------------------------------|-----------------------------------------|-------------------------------------------------|
| Show file                              | `cat file.txt`                          | `Get-Content file.txt` (`gc` alias)            |
| First 20 lines                         | `head -n 20 file.log`                   | `Get-Content file.log -TotalCount 20`          |
| Last 50 lines                          | `tail -n 50 file.log`                   | `Get-Content file.log -Tail 50`                |
| Follow log                             | `tail -f file.log`                      | `Get-Content file.log -Wait`                   |
| Filter lines containing “ERROR”        | `grep "ERROR" file.log`                 | `Select-String -Path file.log -Pattern "ERROR"`|
| Recursive text search                  | `grep -rn "TODO" .`                     | `Select-String -Path .\* -Pattern "TODO" -Recurse` |

### Processes & Network

| Task                            | Bash / Linux                          | PowerShell                                           |
|---------------------------------|---------------------------------------|------------------------------------------------------|
| List processes                  | `ps aux`                              | `Get-Process`                                       |
| Kill process by PID            | `kill 1234`                           | `Stop-Process -Id 1234`                             |
| Find process using port 8080   | `lsof -i :8080` / `netstat -tulpn`    | `netstat -ano \| Select-String ":8080"`             |
| Ping host                      | `ping example.com`                    | `Test-Connection example.com`                       |

### Misc

| Task                            | Bash                                  | PowerShell                                  |
|---------------------------------|---------------------------------------|---------------------------------------------|
| Environment variable read       | `echo $HOME`                          | `echo $env:USERPROFILE`                     |
| Environment variable set        | `export FOO=bar`                      | `$env:FOO = "bar"`                          |
| Current user                    | `whoami`                              | `$env:USERNAME` or `whoami` (alias)        |
| Date / time                     | `date`                                | `Get-Date`                                  |

Diese Tabelle kannst du schnell überfliegen, wenn du zwischen Git Bash/WSL und PowerShell wechselst.

---
