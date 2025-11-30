# Winget Commands 📦

A small collection of useful `winget` commands for managing software on Windows from the command line.

---

## 🧠 Basics

- **Winget** is the Windows Package Manager.
- It allows you to **install**, **upgrade**, **remove** and **search** applications from the command line.
- Most commands should be executed from:
  - **Windows Terminal**
  - **PowerShell**
  - or **Command Prompt (CMD)**

For help:
```
powershell
winget --help
```
---

## 📋 List Installed Packages
```
powershell
# List all installed packages that winget knows about
winget list

# Search for a specific package in the installed list (e.g. Git)
winget list git
```
---

## 🔄 Upgrading Packages

### Show Available Upgrades
```
powershell
# Show all packages that have an available update
winget upgrade
```
- Use this first to see *what* will be updated.
- Each entry has an **Id**, **Name** and current/new **Version**.

### Upgrade a Single Package
```
powershell
# Upgrade a specific package by Id or Name
winget upgrade Git.Git
# or
winget upgrade git
```
Steps for Git example:

1. Find the exact package Id:

   ```powershell
   winget list git
   ```

2. Upgrade that specific package:

   ```powershell
   winget upgrade Git.Git
   ```

### Upgrade All Packages at Once

> Tipp: In der Regel als **Administrator** starten (Terminal als „Run as administrator“).
```
powershell
# Upgrade all packages that have updates
winget upgrade --all
```
Disable interaction (no prompts):
```
powershell
winget upgrade --all --disable-interactivity
```
- Useful for scripted updates or when you want a fully unattended upgrade.

---

## 🔍 Search & Inspect Packages

### Search for Packages
```
powershell
# Search by name
winget search git

# Search with a specific source (optional)
winget search --source winget git
```
### Show Package Details
```
powershell
# Show detailed information for a package (e.g. Git)
winget show git

# Or use the full Id
winget show Git.Git
```
---

## ➕ Installing Packages
```
powershell
# Install a package by Id
winget install Git.Git

# Install by name (winget will try to resolve to an Id)
winget install git

# Install a specific version (if available)
winget install Git.Git --version 2.45.0

# Silent / unattended install (if the package supports it)
winget install Git.Git --silent
```
Common useful options:

- `--silent` / `--silent-with-progress` – minimize installer UI
- `--accept-package-agreements` – auto-accept package license
- `--accept-source-agreements` – auto-accept source agreements

---

## ❌ Uninstalling Packages
```
powershell
# Uninstall a package by Id
winget uninstall Git.Git

# Uninstall by name
winget uninstall "Git"
```
---

## ⚙️ Managing Sources
```
powershell
# List all configured sources
winget source list

# Add a new source (example)
winget source add --name mySource --arg https://example.com/source/index.json

# Update all sources
winget source update

# Remove a source
winget source remove mySource
```
---

## 🧪 Winget & WSL (Hint)

`winget` itself runs on Windows, not inside WSL.  
Useful pattern:

- Use `winget` on **Windows** to install tools like:
    - `Git`, `Docker Desktop`, `VS Code`, `Windows Terminal`, `WSL` support, etc.
- Use `apt`, `dnf`, `pacman` etc. **inside WSL** to manage Linux packages.

Example: install WSL-related components on Windows:
```
powershell
# Install WSL (if not already installed)
wsl --install

# Optional: install a specific distribution (example: Ubuntu)
wsl --install -d Ubuntu
```
(Actual Linux package management inside WSL is then done with `apt`, `yum`, etc., not `winget`.)

---
