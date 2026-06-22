# Lessons: Keeping a Local Git Folder Up to Date with GitHub

## Purpose

This document captures the Git/GitHub lessons from our conversation so I can quickly relearn how to confirm that a local project folder is connected to the correct GitHub repository and up to date with the latest checked-in files.

The example repo used in this note is:

```text
https://github.com/example-user/example-project.git
```

The example local project folder is:

```powershell
C:\Projects\example-project
```

---

## Core Concept

Git is the local version-control tool.

GitHub is the remote hosting service where the repo lives online.

When working from multiple computers, GitHub should be treated as the shared source of truth. Each computer has its own local copy, and each copy needs to regularly sync with GitHub.

The basic habit is:

```powershell
git pull
```

before starting work, and:

```powershell
git add .
git commit -m "Describe what changed"
git push
```

when finished.

---

## Step 1: Confirm the Local Folder Is Connected to the Correct GitHub Repo

From inside the local project folder:

```powershell
cd C:\Projects\example-project
git remote -v
```

Expected output:

```text
origin  https://github.com/example-user/example-project.git (fetch)
origin  https://github.com/example-user/example-project.git (push)
```

### What This Means

This confirms that the local folder is connected to the GitHub repo:

```text
https://github.com/example-user/example-project.git
```

The word `origin` is the default name Git uses for the remote repository.

- `(fetch)` means this is where Git downloads updates from.
- `(push)` means this is where Git uploads commits to.

If the remote URL is wrong, update it with:

```powershell
git remote set-url origin https://github.com/example-user/example-project.git
```

---

## Step 2: Fetch the Latest Information from GitHub

Run:

```powershell
git fetch origin
```

### What `git fetch` Does

`git fetch` contacts GitHub and downloads information about what has changed on the remote repo.

Important: `git fetch` does **not** change the files in the working folder. It only updates Git's knowledge of what exists on GitHub.

This makes it a safe command to run when checking whether the local folder is behind GitHub.

---

## Step 3: Check Local Status

Run:

```powershell
git status
```

In our case, the output was:

```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

### What This Means

This is the ideal result.

It means:

1. The current local branch is `main`.
2. The local `main` branch matches GitHub's `origin/main`.
3. There are no uncommitted local file changes.
4. The local folder already has the latest checked-in files from GitHub for the `main` branch.

In plain English:

> This computer is up to date with GitHub for the `main` branch.

---

## Step 4: Pull Updates If Needed

If `git status` says the branch is behind GitHub, run:

```powershell
git pull
```

or explicitly:

```powershell
git pull origin main
```

### What `git pull` Does

`git pull` is essentially:

```powershell
git fetch
git merge
```

It downloads changes from GitHub and applies them to the local working folder.

Use this when Git says your branch is behind the remote branch.

---

## Important Status Messages and What They Mean

### Up to Date

```text
Your branch is up to date with 'origin/main'.
nothing to commit, working tree clean
```

Meaning:

The local folder is current and clean.

No action needed.

---

### Behind Remote

```text
Your branch is behind 'origin/main' by 3 commits, and can be fast-forwarded.
```

Meaning:

GitHub has newer commits that this computer does not have yet.

Run:

```powershell
git pull
```

---

### Ahead of Remote

```text
Your branch is ahead of 'origin/main' by 1 commit.
```

Meaning:

This computer has local commits that have not been pushed to GitHub yet.

Run:

```powershell
git push
```

---

### Local Changes Exist

```text
Changes not staged for commit
```

or:

```text
Changes to be committed
```

Meaning:

There are local changes in the working folder.

Before pulling from GitHub, decide whether to keep, commit, stash, or discard those changes.

---

## If There Are Local Changes

Before pulling, always run:

```powershell
git status
```

If there are local file changes, choose one of these paths.

---

### Option A: Keep Local Changes Temporarily with Stash

Use this when you are not ready to commit but want to pull from GitHub safely.

```powershell
git stash
git pull
git stash pop
```

What this does:

1. `git stash` temporarily saves local changes.
2. `git pull` updates the folder from GitHub.
3. `git stash pop` reapplies the local changes.

This is usually the safest option when unsure.

---

### Option B: Commit Local Changes First

Use this when the local work is valid and should be saved into Git history.

```powershell
git add .
git commit -m "Save local changes"
git pull
git push
```

This saves the local work, then syncs with GitHub.

---

### Option C: Discard Local Changes

Use this only when the local changes are not needed.

```powershell
git reset --hard
git pull
```

Warning:

`git reset --hard` deletes uncommitted local changes.

Do not use it unless you are sure you do not need those edits.

---

## Understanding the Forced Update Message

During `git fetch origin`, this appeared:

```text
+ abc1234...def5678 feature/example-branch -> origin/feature/example-branch  (forced update)
```

### What This Means

A different GitHub branch named:

```text
feature/example-branch
```

had its history rewritten or force-updated.

This does **not** mean the local `main` branch is out of date.

Since the current branch was `main`, and Git said:

```text
Your branch is up to date with 'origin/main'.
```

there was nothing to fix on `main`.

Unless actively working on the `feature/example-branch` branch, this message can be ignored.

---

## Recommended Multi-Computer Workflow

When using the same repo from multiple computers, use this routine.

---

### Before Starting Work

```powershell
cd C:\Projects\example-project
git fetch origin
git status
git pull
```

This confirms the local folder is connected, checks status, and pulls the latest GitHub changes.

---

### While Working

Check status often:

```powershell
git status
```

See what changed:

```powershell
git diff
```

---

### When Finished Working

```powershell
git status
git add .
git commit -m "Describe what changed"
git push
```

This saves changes locally and sends them to GitHub so the next computer can pull them.

---

## Quick Reference Commands

### Check Remote Repo

```powershell
git remote -v
```

### Check Current Branch

```powershell
git branch --show-current
```

### Fetch Latest GitHub Info

```powershell
git fetch origin
```

### Check Whether Local Folder Is Current

```powershell
git status
```

### Pull Latest Files

```powershell
git pull
```

### Save Current Work

```powershell
git add .
git commit -m "Describe what changed"
```

### Push Work to GitHub

```powershell
git push
```

### Temporarily Save Local Changes

```powershell
git stash
```

### Reapply Stashed Changes

```powershell
git stash pop
```

---

## My Personal Rule

For this repo, before doing any work on any computer:

```powershell
cd C:\Projects\example-project
git fetch origin
git status
git pull
```

When done working:

```powershell
git status
git add .
git commit -m "Describe what changed"
git push
```

The main habit to remember:

> Pull before starting. Push when finished.

---

## What I Learned

- `git remote -v` confirms the local folder is connected to the correct GitHub repo.
- `git fetch origin` safely checks GitHub for updates without changing local files.
- `git status` tells whether the local branch is current, behind, ahead, or has local changes.
- `git pull` updates the local folder with the latest checked-in files from GitHub.
- `working tree clean` means there are no uncommitted local file changes.
- `up to date with 'origin/main'` means the local `main` branch matches GitHub's `main` branch.
- A forced update on another branch does not matter unless I am working on that branch.
- When working across multiple computers, GitHub should be the sync point.
- The safest routine is to pull before starting work and push after finishing work.

---

## Current Confirmed State from the Conversation

The local folder:

```powershell
C:\Projects\example-project
```

is connected to:

```text
https://github.com/example-user/example-project.git
```

The current branch is:

```text
main
```

The final status showed:

```text
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

Conclusion:

> The local `main` branch on this computer already has the latest checked-in version from GitHub.
