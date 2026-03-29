# Git Commands 🧭  
A curated, structured, and developer‑friendly reference of essential Git commands for everyday work.

---

# 1. 🌿 Branching & Feature Branch Workflows

## 1.1 Creating Branches

```bash
# Create and switch to a new branch
git checkout -b feature/my-new-feature

# Modern alternative
git switch -c feature/my-new-feature
```

Create a branch from a specific base:

```bash
git checkout -b feature/my-new-feature master
```

Push the new branch to remote:

```bash
git push -u origin feature/my-new-feature
```

---

## 1.2 Keeping Your Feature Branch Up to Date

### Option A — Merge (safe, keeps merge commits)

```bash
git checkout feature/my-feature
git fetch origin
git merge origin/master
```

### Option B — Rebase (clean, linear history)

```bash
git checkout feature/my-feature
git fetch origin
git rebase origin/master
```

Conflict handling:

```bash
git rebase --continue
```

Abort if needed:

```bash
git rebase --abort
```

---

## 1.3 Merging a Feature Branch into `master`

Standard workflow:

```bash
git checkout master
git pull origin master
git merge feature/jenkins-ci
git push origin master
```

### Step-by-step

1. Switch to master  
   ```bash
   git checkout master
   ```

2. Pull the latest state  
   ```bash
   git pull origin master
   ```

3. Merge the feature branch  
   ```bash
   git merge feature/jenkins-ci
   ```

4. Push the updated master  
   ```bash
   git push origin master
   ```

---

## 1.4 Preparing Your Feature Branch Before Merge

Check your working directory:

```bash
git status
```

If changes are uncommitted:

```bash
git add .
git commit -m "Finalize Jenkins CI pipeline integration"
```

---

## 1.5 Resolving Merge Conflicts

After resolving conflicts manually:

```bash
git add <file>
git commit
git push origin master
```

---

## 1.6 Cleaning Up Branches

Delete locally:

```bash
git branch -d feature/jenkins-ci
```

Delete on remote:

```bash
git push origin --delete feature/jenkins-ci
```

---

## 1.7 Compact Merge Workflow

```bash
git status
git checkout master
git pull origin master
git merge feature/jenkins-ci
git push origin master
```

---

## 1.8 Branching Best Practices

- Keep feature branches small and focused  
- Rebase frequently to reduce conflict size  
- Always pull latest master before merging  
- Delete branches after merging  
- Use consistent naming conventions:  
  - `feature/...`  
  - `bugfix/...`  
  - `hotfix/...`  
  - `chore/...`

---

# 2. 🧱 Stash Commands

### References  
- Atlassian Tutorial – Git Stash  
- Useful Tricks with Git Stash – freeCodeCamp  

### Basic Usage

```bash
git stash
git stash save "My message"
git stash list
git stash show
git stash show stash@{2}
```

### Applying & Removing Stashes

```bash
git stash apply
git stash apply stash@{2}
git stash pop
git stash drop
git stash clear
```

---

# 3. 🧹 Discarding Local Changes

```bash
git stash
git checkout -- <file>
git reset --hard
```

Reset to a specific commit:

```bash
git reset --hard <commit-hash>
```

Remove file from staging:

```bash
git rm --cached <filename>
```

---

# 4. 🔄 Rebasing

### References  
- Atlassian: Merging vs Rebasing  
- Git Book: Rebasing  

```bash
git rebase master
git rebase --abort
git rebase --continue
```

---

# 5. 🧰 Repository Maintenance

If you see:

```
Auto packing the repository...
error: failed to run repack...
```

Run:

```bash
git gc --aggressive
```

---

# 6. 🔗 Remote Repository Information

Show remote URL:

```bash
git config --get remote.origin.url
```

Show full remote info:

```bash
git remote show origin
```

---

# 7. 🌍 Remote Origin Management

```bash
git remote -v
git remote set-url origin git://new.url.here
git remote rename origin origin-old
git remote remove origin
```

---

# 8. 🏷️ Tagging

```bash
git tag -l
git tag RV-0.0.1 -m "My tag message"
git push --follow-tags
git push --tags
git push origin <tagname>
```

---

# 9. 📦 Moving or Renaming Files

```bash
git mv <source> <destination>
```

---

# 10. 🚩 Correcting the Last Commit (Before Pushing)

Stage corrections:

```bash
git add <filename>
# or
git add .
```

Amend the commit:

```bash
git commit --amend --no-edit
```


---

