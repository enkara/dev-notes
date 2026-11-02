# 🧭 SVN Commands – Cheat Sheet

A curated list of useful **Subversion (SVN)** commands for everyday development.

---

## 📚 References

- [Apache Subversion – Official Documentation](https://subversion.apache.org/docs/)
- [Version Control with Subversion – SVN Book](https://svnbook.red-bean.com/)

---

## 🧠 Basic Concepts

A common SVN repository layout looks like this:

```text
trunk/      # Main development line
branches/  # Feature, release or maintenance branches
tags/       # Snapshots, often used for releases
```

Important terms:

- **Repository**: the central storage on the server
- **Working Copy**: your local checkout on your machine
- **Revision**: a unique repository state, e.g. `r1234`
- **trunk**: the main development line
- **branch**: a copied repository path, often used for feature work
- **tag**: a snapshot, often used for releases

---

## 🔍 Help & Info

```bash
# Show general help
svn help

# Show help for a specific command
svn help <command>

# Show SVN version
svn --version

# Show information about the current working copy
svn info
```

Useful fields from `svn info`:

- `URL:` shows which repository path your working copy points to
- `Repository Root:` shows the root URL of the repository
- `Revision:` shows the current revision of your working copy

---

## 📥 Checkout, Update & Switch

```bash
# Check out a repository or repository path
svn checkout <REPO_URL> .

# Example: check out trunk
svn checkout <REPO_URL>/trunk .

# Update your working copy to the latest revision
svn update

# Switch your working copy to another branch or trunk
svn switch <BRANCH_URL>

# Example: switch back to trunk
svn switch <REPO_URL>/trunk
```

---

## 🧭 Repository Root with `^/`

Inside an SVN working copy, `^/` can be used as a shortcut for the repository root URL.

```bash
# Show repository root
svn info --show-item repos-root-url

# List branches
svn list ^/branches

# Switch to trunk
svn switch ^/trunk

# Switch to a branch
svn switch ^/branches/<BRANCH_NAME>
```

> This assumes your repository layout matches these relative paths, e.g. `trunk`, `branches` and `tags` directly below the repository root.

---

## 📊 Status & Diff

```bash
# Show working copy status
svn status

# Also show ignored files
svn status --no-ignore

# Show local changes
svn diff

# Show diff between two revisions
svn diff -r <REV1>:<REV2>

# Show changes of a specific revision
svn diff -c <REV>
```

Common status codes:

| Code | Meaning     | Explanation                               |
|------|-------------|-------------------------------------------|
| `A`  | Added       | Newly added, not committed yet            |
| `M`  | Modified    | Locally changed                           |
| `D`  | Deleted     | Locally deleted, commit pending           |
| `?`  | Unversioned | Not under version control                 |
| `!`  | Missing     | Missing locally, but expected by SVN      |
| `I`  | Ignored     | Ignored by SVN ignore rules               |
| `C`  | Conflicted  | Conflict after update or merge            |
| `R`  | Replaced    | Deleted and replaced with a new file      |
| `X`  | Externals   | Included via `svn:externals`              |
| `~`  | Obstructed  | Path is blocked or has an unexpected type |

---

## ➕ Add, Delete, Move & Copy

```bash
# Add a new file or directory to version control
svn add <path>

# Delete a file or directory
svn delete <path>

# Move or rename a file/directory while keeping history
svn move <old_path> <new_path>

# Copy a file, directory or repository path
svn copy <source> <destination>
```

Example:

```bash
svn add src/NewFile.java
svn delete src/OldFile.java
svn move src/OldName.java src/NewName.java
```

---

## 💾 Commit Changes

```bash
# Commit all local changes
svn commit -m "Short description of the change"

# Commit only specific files or directories
svn commit <path1> <path2> -m "Short description of the change"
```

Example commit messages:

```bash
svn commit -m "Fix login validation"
svn commit -m "Add customer export"
svn commit -m "Merge trunk into feature branch"
```

---

## 🔁 Revert Local Changes

```bash
# Revert local changes to a file or directory
svn revert <path>

# Revert all local changes recursively
svn revert -R .
```

> Warning: `svn revert` permanently discards local changes. It does not undo already committed revisions.

---

## 🧾 History & Blame

```bash
# Show commit history
svn log

# Show the last 10 log entries
svn log -l 10

# Show log for a specific path
svn log <path>

# Show details for a specific revision
svn log -v -r <REV>

# Show who last changed each line
svn blame <file>
```

Examples:

```bash
svn log -l 5
svn log -v -r 1234
svn blame src/App.java
```

---

## 🧹 Cleanup & Working Copy Issues

```bash
# Repair working copy metadata, e.g. after interrupted operations
svn cleanup

# Then update again
svn update
```

Typical cases for `svn cleanup`:

- an update was interrupted
- a commit was interrupted
- the working copy is locked
- SVN reports `working copy locked`

---

## 🔒 Locking & Unlocking

Some SVN repositories use locks, often for binary files or documents.

```bash
# Lock a file
svn lock <path> -m "Reason"

# Unlock a file
svn unlock <path>

# Force a lock if needed
svn lock --force <path>
```

> Use `--force` only when you are sure the existing lock is no longer needed.

---

## 🌿 Branching

In SVN, branches are usually cheap copies under `/branches`.

### List Branches

```bash
# List branches
svn list <REPO_URL>/branches

# List branches with details
svn list -v <REPO_URL>/branches

# Inside a working copy: list branches relative to repository root
svn list ^/branches
```

### Create a Branch

```bash
# Create a feature branch from trunk
svn copy ^/trunk ^/branches/<BRANCH_NAME> -m "Create branch <BRANCH_NAME>"
```

Example:

```bash
svn copy ^/trunk ^/branches/feature-PROJ-123-login-fix -m "Create branch feature-PROJ-123-login-fix"
```

### Switch to a Branch

```bash
# Switch current working copy to a branch
svn switch ^/branches/<BRANCH_NAME>

# Switch back to trunk
svn switch ^/trunk
```

Check current location:

```bash
svn info
```

Look for the `URL:` line to see whether you are on `trunk`, a branch or a tag.

---

## 🏷️ Tags

In SVN, tags are usually cheap copies under `/tags`.

```bash
# Create a tag from trunk
svn copy ^/trunk ^/tags/<TAG_NAME> -m "Tag <TAG_NAME>"

# Create a tag from a branch
svn copy ^/branches/<BRANCH_NAME> ^/tags/<TAG_NAME> -m "Tag <TAG_NAME>"
```

Example:

```bash
svn copy ^/trunk ^/tags/release-1.0.0 -m "Tag release-1.0.0"
```

> Tags should usually not be modified after creation.

---

## 🔀 Merging

### Merge Trunk into Your Current Branch

```bash
# Make sure you are on your branch
svn info

# Update working copy
svn update

# Merge latest trunk changes into your branch
svn merge ^/trunk

# Review and commit
svn status
svn diff
svn commit -m "Merge trunk into branch"
```

### Merge a Feature Branch into Trunk

```bash
# Switch to trunk
svn switch ^/trunk

# Update trunk
svn update

# Merge feature branch into trunk
svn merge ^/branches/<BRANCH_NAME>

# Review and commit
svn status
svn diff
svn commit -m "Merge <BRANCH_NAME> into trunk"
```

> Note: `svn merge --reintegrate` was relevant in older SVN workflows. In current SVN versions, a regular `svn merge <branch_url>` is usually
> sufficient.

---

## ⚔️ Conflict Resolution

```bash
# Show conflicts
svn status

# After manually resolving the conflict, mark it as resolved
svn resolve --accept=working <path>

# Take your version completely
svn resolve --accept=mine-full <path>

# Take the incoming version completely
svn resolve --accept=theirs-full <path>
```

Typical flow:

```bash
svn status
# Edit the conflicted file manually
svn resolve --accept=working <path>
svn status
svn commit -m "Resolve merge conflict"
```

> Use `mine-full` and `theirs-full` carefully, because one side will be taken completely.

---

## 🧰 Properties & Ignore Rules

```bash
# Set ignore rule on a directory
svn propset svn:ignore "node_modules" .

# Show ignore rules
svn propget svn:ignore .

# List all properties
svn proplist -v .
```

Set multiple ignore entries:

```bash
svn propset svn:ignore "node_modules
dist
.env
.idea" .
```

Then commit:

```bash
svn commit -m "Update svn ignore rules"
```

---

## ↩️ Revert a Committed Revision

An already committed revision is reverted in SVN by using a **reverse merge**.

### 1. Find the Revision

```bash
svn log -v
```

Example: the wrong revision is `r1234`.

### 2. Run Reverse Merge

Inside the target path, e.g. in `trunk`:

```bash
svn merge -c -1234 .
```

The minus before the revision means: reverse this change.

### 3. Review Changes

```bash
svn status
svn diff
```

### 4. Commit the Revert

```bash
svn commit -m "Revert r1234"
```

> Important: `svn revert` only discards local changes. To undo already committed changes, use a reverse merge.

---

## 📦 Patch as Stash Alternative

SVN does not have a built-in `stash` command like Git. A simple alternative is creating a patch.

```bash
# Save local changes into a patch file
svn diff > my-changes.patch

# Clean working copy
svn revert -R .

# Re-apply the patch later
svn patch my-changes.patch
```

> In JetBrains IDEs such as WebStorm or IntelliJ, you can also use **Shelve / Unshelve** for a stash-like workflow.

---

## ✅ Quick Reference

```bash
svn status
svn update
svn diff
svn add <path>
svn delete <path>
svn move <old_path> <new_path>
svn commit -m "Message"
svn log -l 10
svn info
svn cleanup
svn switch ^/trunk
svn switch ^/branches/<BRANCH_NAME>
svn merge ^/trunk
svn resolve --accept=working <path>
svn revert <path>
```

