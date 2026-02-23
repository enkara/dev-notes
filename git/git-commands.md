# Git Commands 🧭

A curated list of useful Git commands and references for everyday development.

---

## 🧱 Stash Commands

**References:**

- [Atlassian Tutorial – Git Stash (DE)](https://de.atlassian.com/git/tutorials/saving-changes/git-stash)
- [Useful Tricks with Git Stash – freeCodeCamp](https://medium.freecodecamp.org/useful-tricks-you-might-not-know-about-git-stash-e8a9490f0a1a)

### Basic Usage

```bash
# Save all your local work
git stash

# Save with a custom message
git stash save "My message"

# List all stashes (most recent is stash@{0})
git stash list

# Show a summary of changes in the latest stash
git stash show

# Show the changes from a specific stash
git stash show stash@{2}
```

### Applying and Removing Stashes

```bash
# Apply your last stash and keep it
git stash apply

# Apply a specific stash
git stash apply stash@{2}

# Apply and remove it from the stash list
git stash pop

# Drop a stash manually
git stash drop

# Clear all stashes
git stash clear
```

---

## 🧹 Discarding Local Changes

```bash
# Discard all local changes but keep them for reuse
git stash

# Discard changes to a specific file permanently
git checkout -- <file>

# Discard all local changes permanently
git reset --hard
```

If that doesn’t work, reset to a specific commit (only if commits were already pushed):

```bash
git reset --hard <commit-hash>
```

**Remove file from the staging area**<br>  
To remove a file from the index (the staging area) and thus from version control without deleting it locally, you use:

```bash
git rm --cached <filename>
```

---

## 🌿 Branches

```bash
# Show remote branches
git branch -r

# Checkout a branch
git checkout testbranch

# Checkout a remote branch (if multiple remotes)
git checkout -b testbranch origin/test

# Switch back to master/main
git checkout master
```

---

## 🔄 Rebasing

**References:**

- [Merging vs Rebasing – Atlassian (DE)](https://de.atlassian.com/git/tutorials/merging-vs-rebasing)
- [Git Book – Rebasing (DE)](https://git-scm.com/book/de/v1/Git-Branching-Rebasing)

```bash
# Rebase your work
git rebase master

# Abort an in-progress rebase
git rebase --abort

# Continue a paused rebase
git rebase --continue
```

---

## 🧰 Repository Maintenance

If you encounter errors like:

```
Current branch master is up to date.
Auto packing the repository in background for optimum performance.
error: failed to run repack...
```

Try running garbage collection:

```bash
git gc --aggressive
```

---

## 🔗 Remote Repository Information

### Show Remote URL

```bash
git config --get remote.origin.url
```

### Show Full Remote Info

```bash
git remote show origin
```

---

## 🏷️ Tagging

```bash
# Show existing tags
git tag -l

# Create a new tag, e.g. RV-0.0.1
git tag RV-0.0.1 -m "My tag message"

# Push all tags belonging to the current commit to remote
git push --follow-tags

# Push all local tags to remote
git push --tags

# Push a single tags to remote 
git push origin <tagname>

```

**Reference:** [StackOverflow – Push a tag to remote](https://stackoverflow.com/questions/5195859/how-do-you-push-a-tag-to-a-remote-repository-using-git)

---

## 🌍 Remote Origin Management

```bash
# Show connected repositories
git remote -v

# Change remote URL
git remote set-url origin git://new.url.here

# Example:
git remote set-url origin http://myserver:3000/acompany/myrepo.git

# Rename remote
git remote rename origin origin-old

# Remove remote
git remote remove origin
```

---

## 📦 Moving or Renaming Files

```bash
# Move or rename file or directory
git mv <source> <destination>
```

## 🚩Correcting the Last Commit (Before Pushing)
If you have already committed changes but realized you left unwanted code (e.g., sensitive comments or debug logs) in your files, you can fix the commit without creating a new "fix-up" entry in your history.

Prerequisites:
The changes have not been pushed to a remote repository yet.
You have already corrected the files in your working directory.

Step-by-Step Guide:
Stage your corrections:
Add the fixed files to the staging area.

```bash
git add <filename>
# or to stage all changes:
git add .
```
Amend the commit:
Update the last commit with your staged changes. The --no-edit flag keeps the original commit message.

```bash
git commit --amend --no-edit
```
Note: This command effectively replaces the last commit with a new one. Since you haven't pushed yet, this is safe and keeps your git history clean.

---
