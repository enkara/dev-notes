# Experimentation workflow withouth branch history 

How to experiment on a local feature branch and merge to `main` without having the branch history?

This note describes two CLI workflows for running many experiments on a feature branch and later bringing only the final result into `main` **without** keeping the feature branch commit history in `main`.

Assumptions:
- You are currently on `main`
- You have **local uncommitted changes** on `main`
- `origin/main` is your trunk
- You prefer command line

---

## Goal

- Keep experimentation history off `main`
- End up with a clean `main` containing either:
  - **one squashed commit** with the final state, or
  - **one snapshot commit** cherry-picked to `main`
- Keep the feature branch local (unless you explicitly push it)

---

## Pre-flight checks
``` bash
git status
git branch --show-current
git remote -v
```
(Optional) Make sure your view of the remote is current:
```
bash
git fetch origin
```
---

## Workflow A (recommended): local feature branch + squash merge into `main`

This keeps your experiments on a branch and later merges them into `main` as **one single commit**.

### Step 1: move your uncommitted changes off `main` (stash)
``` bash
git switch main
git status
git stash push -u -m "experiments: WIP"
```
### Step 2: create and switch to a new feature branch
``` bash
git switch -c feature/experiments
```
### Step 3: restore your changes on the feature branch
``` bash
git stash pop
```
Now do your experiments and commit as often as you like:
``` bash
git add -A
git commit -m "Try approach X"

git add -A
git commit -m "Refactor"

git add -A
git commit -m "Fix tests"
```
### Step 4: update `main` from `origin/main` (important)

This ensures you squash onto the current trunk state.
``` bash
git switch main
git fetch origin
git reset --hard origin/main
```
### Step 5: squash merge the feature branch into `main`
``` bash
git merge --squash feature/experiments
git commit -m "Initial working version (squashed)"
```
### Step 6: push `main`
``` bash
git push origin main
```
### Afterward: delete the local feature branch (optional)

Once you're confident you don't need it anymore:
``` bash
git branch -D feature/experiments
```
Notes:
- `--squash` means the **feature branch history is not merged into `main`**.
- The feature branch remains local unless you push it.

---

## Workflow B: create a single "snapshot" commit and cherry-pick only that to `main`

This workflow is useful if you want to keep experimenting with many commits, then create one final “snapshot” commit and bring **only that** into `main`.

### Step 1: create and switch to the feature branch (without stashing)

If you have uncommitted changes on `main`, you can directly create a branch; the working tree moves with you:
``` bash
git switch -c feature/experiments
```
Commit freely during experimentation:
``` bash
git add -A
git commit -m "Try approach X"
git commit -am "Refactor (small change)"
```
### Step 2: create one final snapshot commit

When you're happy with the current state:
``` bash
git add -A
git commit -m "Snapshot: initial working version"
```
Copy the commit hash:
``` bash
git rev-parse HEAD
```
### Step 3: reset `main` to `origin/main`
``` bash
git switch main
git fetch origin
git reset --hard origin/main
```
### Step 4: cherry-pick only the snapshot commit onto `main`

Replace `<SNAPSHOT_HASH>` with the hash from Step 2:
``` bash
git cherry-pick <SNAPSHOT_HASH>
```
### Step 5: push `main`
``` bash
git push origin main
```
### Cleanup (optional): delete the local feature branch
``` bash
git branch -D feature/experiments
```
Notes:
- Only the snapshot commit becomes part of `main`.
- All other experiment commits remain on the feature branch only.

---

## Confirmations / guarantees

### “Will my feature branch remain local?”
Yes, unless you run:
``` bash
git push -u origin feature/experiments
```
### “Will `main` contain the feature branch history?”
- Workflow A: **No** (squash merge creates a new commit on `main`)
- Workflow B: **No** (only the cherry-picked snapshot commit is added)

### “Does the experiment history disappear completely?”
Deleting the branch removes the reference. Commits may still exist internally for a while until Git garbage collection cleans them up. If you want to force cleanup (usually not needed):
``` bash
git reflog expire --expire=now --all
git gc --prune=now
```
Use with care.

---

## Troubleshooting

### If `git stash pop` causes conflicts
Resolve conflicts, then:
``` bash
git add -A
git commit -m "Resolve stash conflicts"
```
### If you accidentally committed on `main`
You can still move that commit to a new branch and reset `main` back to `origin/main`. (Not covered here.)
```
