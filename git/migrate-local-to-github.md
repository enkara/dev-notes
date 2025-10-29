# Migrating Local Git Repositories (Gitea) to GitHub — Manual Procedure

This guide explains how to manually migrate existing local Git repositories
(from a Gitea server or other source) to GitHub while keeping **full commit history**.

---

## 🧭 Example Repository

- **Local origin:** `http://nihat@aserver:3000/acompany/project-x.git`
- **Local path:** `/c/DevProjects/project-x`
- **Target (GitHub):** `https://github.com/enkara/project-x.git`

---

## 1. Create a Private Repository on GitHub

1. Go to [https://github.com/new](https://github.com/new)
2. Choose:
   - **Owner:** `enkara`
   - **Repository name:** `ti`
3. Select **Private**
4. Leave all initialization options **unchecked**:
   - No README
   - No `.gitignore`
   - No license
5. Click **Create repository**

---

## 2. Verify the Local Repository

```bash
cd /d/DevProjects/ti
git status
git remote -v
```

Expected:

```bash

origin  http://nihat@aserver:3000/acompany/project-x.git (fetch)
origin  http://nihat@aserver:3000/acompany/project-x.git (push)
```

## 3. Add the GitHub Remote

Add GitHub as an additional remote (keep your Gitea remote as origin):

```bash
git remote add github https://github.com/enkara/project-x.git
```

Confirm:

```bash
git remote -v
```

## 4. Push to GitHub (Preserve Full History)

```bash
git push github master
```

or, if your main branch is named main:

```bash
git push github main
```

If authentication is required, use a GitHub Personal Access Token (PAT)
instead of your password:

https://github.com/settings/tokens

## 5. Verify on GitHub

After the push completes, visit:

https://github.com/enkara/ti

You should now see:

All commits and full history

Correct branch structure

Private visibility (lock icon)

## 6. Optional — Push to Both Remotes Automatically

If you want to push changes to both GitHub and Gitea with a single command:

```bash
git remote set-url --add --push origin http://nihat@aserver:3000/acompany/project-x.git
git remote set-url --add --push origin https://github.com/enkara/project-x.git
```

Then you can simply use:

```bash
git push origin master
```

→ This pushes to both repositories simultaneously.

## 7. Ensure GitHub Recognizes Your Commits

Check your local commit email:

```bash
git config user.email
```

Add that email to your GitHub account:

https://github.com/settings/emails

Only verified emails are linked to your GitHub activity graph.

# ✅ Summary

Step Command Description

- 1 Create a private GitHub repo (no README)
- 2 git remote -v Check existing remotes
- 3 git remote add github ... Add GitHub as a new remote
- 4 git push github master Push full history to GitHub
- 5 – Verify repository on GitHub
- 6 git remote set-url --add --push origin ... Optional: push to both remotes
- 7 – Verify your commit email in GitHub
