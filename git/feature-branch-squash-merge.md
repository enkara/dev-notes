# Experimentation workflow withouth branch history 

How to experiment on a local feature branch and merge to `main` without having the branch history?

This note describes two CLI workflows for running many experiments on a feature branch and later bringing only the final result into `main` **without** keeping the feature branch commit history in `main`.

## When to use which workflow

### Workflow A (recommended): `git merge --squash`
Use when:
- You want the final result on `main` as **one clean commit**
- You don’t care about keeping any experiment commits in `main`
- You want a straightforward, reliable flow

### Workflow B: Snapshot + `git cherry-pick`
Use when:
- You want to keep experimenting and then create an explicit **final snapshot commit**
- You want to transfer **only that single snapshot commit** to `main`

---

## Assumptions

- You are on `main`
- You have **local uncommitted changes** on `main`
- There are **no local commits on `main`**
- `origin/main` is the trunk
- You prefer command line

---

## Pre-flight checks
``` bash
git status
git branch --show-current
git fetch origin
```
---

## Workflow A (recommended): local feature branch + squash merge into `main`

### 1) Move uncommitted changes off `main` (stash)
``` bash
git switch main
git status
git stash push -u -m "experiments: WIP"
```
### 2) Create and switch to a feature branch
``` bash
git switch -c feature/experiments
```
### 3) Restore changes on the feature branch
``` bash
git stash pop
```
Commit as often as you want:
``` bash
git add -A
git commit -m "Try approach X"

git add -A
git commit -m "Refactor"

git add -A
git commit -m "Fix tests"
```
### 4) Reset local `main` to the current `origin/main`
``` bash
git switch main
git fetch origin
git reset --hard origin/main
```
### 5) Squash-merge the feature branch into `main` (one commit)
``` bash
git merge --squash feature/experiments
git commit -m "Initial working version (squashed)"
```
### 6) Push `main`
``` bash
git push origin main
``` 
### 7) Optional cleanup: delete the local feature branch
``` bash
git branch -D feature/experiments
```
Notes:
- `--squash` creates a new commit on `main` and **does not merge** the feature branch history.
- The feature branch stays local unless you explicitly push it.

---

## Workflow B: snapshot commit + cherry-pick only that to `main`

### 1) Create and switch to a feature branch (working tree moves with you)

If your changes are currently uncommitted on `main`, you can branch immediately:
``` bash
git switch -c feature/experiments
```
Commit freely during experimentation:
``` bash
git add -A
git commit -m "Try approach X"
git add -A
git commit -m "Refactor"
```
### 2) Create a final snapshot commit

When you’re happy with the current state:
``` bash
git add -A
git commit -m "Snapshot: initial working version"
git rev-parse HEAD
```
Copy the printed hash.

### 3) Reset local `main` to the current `origin/main`
``` bash
git switch main
git fetch origin
git reset --hard origin/main
```
### 4) Cherry-pick only the snapshot commit onto `main`

Replace `<SNAPSHOT_HASH>` with your hash:
``` bash
git cherry-pick <SNAPSHOT_HASH>
```
### 5) Push `main`
``` bash
git push origin main
```
### 6) Optional cleanup: delete the local feature branch
``` bash
git branch -D feature/experiments
```
Notes:
- Only the snapshot commit becomes part of `main`.
- All experiment commits remain only on the feature branch.

---

## Confirmations

### Will the feature branch remain local?
Yes, unless you run:
``` bash
git push -u origin feature/experiments
```
### Will `main` contain the feature branch history?
- Workflow A: No (squash merge produces a single new commit on `main`)
- Workflow B: No (only the cherry-picked snapshot commit is added)

### Will the experiment history “go to the trash”?
Deleting the branch removes the reference. Commits can remain internally for a while until Git garbage collection prunes them.

Optional (use with care):
``` bash
git reflog expire --expire=now --all
git gc --prune=now
```
---

## Troubleshooting

### If `git stash pop` causes conflicts
Resolve conflicts, then:
``` bash
git add -A
git commit -m "Resolve stash conflicts"
```
### You want to double-check what will be squashed
Before squashing, compare branches:
``` bash
git log --oneline --decorate --graph --left-right main...feature/experiments
git diff main...feature/experiments
```
### If you accidentally committed on `main`
You can still move that commit to a new branch and reset `main` back to `origin/main`. (Not covered here.)

