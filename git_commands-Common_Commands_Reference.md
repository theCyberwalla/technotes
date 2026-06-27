# Git Commands: Common Commands Reference

## Purpose

This document is a plain-English reference for the Git (and GitHub `gh`) commands
that come up most often in everyday work. Each command includes what it does, how
it works, and when to use it. The examples are drawn from real tasks — setting up a
new repo, ignoring files, connecting to GitHub, merging an existing remote, and
keeping things in sync.

For the day-to-day "is my folder up to date?" routine, see the companion note
`git_lessons-Update_Local_Folder.md`. This file focuses on the commands themselves.

---

## Mental Model

- **Git** is the version-control tool that runs **locally** on your computer.
- **GitHub** is the **remote** website where a copy of the repo is hosted.
- A **commit** is a saved snapshot of your files at a point in time.
- A **branch** (usually `main`) is a line of commits.
- **`origin`** is the default nickname for your GitHub remote.

The normal flow of a change moves through three places:

```text
Working Directory  ->  Staging Area  ->  Local Repo (commit)  ->  GitHub (push)
     (edit)            (git add)          (git commit)            (git push)
```

---

## 1. Starting a Repository

### `git init`

```bash
git init
```

Creates a brand-new, empty Git repository in the current folder. It makes a hidden
`.git` directory that stores all history and settings. Nothing is tracked yet — you
still have to add and commit files. Run this once, at the very start of a project.

### `git clone`

```bash
git clone https://github.com/user/repo.git
```

Downloads an **existing** GitHub repo to your computer, including its full history,
and automatically sets up `origin` pointing back to GitHub. Use this instead of
`git init` when the repo already exists online and you just want a local copy.

---

## 2. The Save Cycle: status -> add -> commit

### `git status`

```bash
git status
```

Shows the current state: which branch you are on, which files changed, what is
staged for the next commit, and whether you are ahead of or behind GitHub. This is
the safest command in Git — it changes nothing. Run it constantly.

### `git add`

```bash
git add .              # stage everything that changed
git add index.html     # stage one specific file
```

Moves changes into the **staging area**, marking them to be included in the next
commit. The `.` means "everything in this folder and below." Staging lets you
choose exactly what goes into a commit instead of committing everything at once.

### `git commit`

```bash
git commit -m "Describe what changed"
```

Permanently records the staged changes as a snapshot in the local repository. The
`-m` flag supplies the commit message inline. A good message says *what* changed and
*why*. This saves to your computer only — it does **not** touch GitHub until you push.

This is what an "initial check-in" is: the very first `git add` + `git commit` after
`git init`, which becomes the first snapshot (the "root commit") of the project.

---

## 3. Ignoring Files

### `.gitignore`

`.gitignore` is a plain text file (not a command) that lists files and folders Git
should **not** track. One pattern per line:

```text
html/            # ignore the entire html folder
index.html       # ignore this specific file
manifest.json    # ignore this specific file
*.log            # ignore every file ending in .log
```

Anything matching these patterns is invisible to `git add .` and stays out of
commits. Use it for generated output, build artifacts, secrets, or large files you
don't want online.

> Note: `.gitignore` only affects files that aren't **already** tracked. If a file
> was committed before you ignored it, remove it from tracking with
> `git rm --cached <file>` (this keeps the file on disk but stops tracking it).

---

## 4. Connecting to GitHub (Remotes)

### `git remote add`

```bash
git remote add origin https://github.com/user/repo.git
```

Links your local repo to a GitHub repo, giving it the nickname `origin`. After this,
`origin` is shorthand for that GitHub URL in push/pull/fetch commands. You do this
once after `git init` when the GitHub repo already exists separately.

### `git remote -v`

```bash
git remote -v
```

Lists the remotes and their URLs. `(fetch)` is where Git downloads from, `(push)` is
where it uploads to. Use it to confirm you're connected to the right repo.

### `git remote set-url`

```bash
git remote set-url origin https://github.com/user/new-repo.git
```

Changes where `origin` points without removing and re-adding it. Handy if the repo
was renamed or moved.

---

## 5. Syncing: fetch, pull, push

### `git fetch`

```bash
git fetch origin
```

Downloads the latest information about what's on GitHub **without** changing any of
your working files. It only updates Git's knowledge of the remote (e.g. `origin/main`).
Safe to run any time to "check" before merging.

### `git pull`

```bash
git pull                # pull current branch
git pull origin main    # be explicit about remote and branch
```

Updates your local folder with the latest from GitHub. It is really two steps in one:
`git fetch` (download) followed by `git merge` (apply). Use it before starting work so
you have everyone's latest changes.

### `git push`

```bash
git push                       # push current branch
git push -u origin main        # first push: set up tracking
```

Uploads your local commits to GitHub. The `-u` (short for `--set-upstream`) on the
**first** push links your local `main` to `origin/main`, so afterward a plain
`git push` / `git pull` knows where to go. Use it when you've committed work and want
it on GitHub.

---

## 6. Merging Histories

### `git merge`

```bash
git merge origin/main --no-edit
```

Combines another branch's commits into your current branch. `--no-edit` accepts the
default merge message instead of opening an editor. If the same lines changed in both
places, you get a **merge conflict** to resolve by hand.

### `git merge --allow-unrelated-histories`

```bash
git merge origin/main --allow-unrelated-histories --no-edit
```

A special case. Normally Git refuses to merge two repos that have **no common
history** (for example, a fresh local `git init` and a GitHub repo that was created
separately with its own commits). This flag tells Git: "I know these started apart —
stitch them together anyway." This is exactly what we used to join the local notes
with the existing `technotes` repo on GitHub. After merging, you `git push` the
combined result.

---

## 7. Looking at History

### `git log`

```bash
git log                          # full history
git log --oneline                # compact: one line per commit
git log --oneline --all          # include every branch
git log --oneline -5             # just the last 5 commits
```

Shows the commit history — who changed what and when. `--oneline` is the most
readable for a quick scan. Each commit has a short hash (like `54e796b`) you can use
to refer to it.

### `git diff`

```bash
git diff                 # unstaged changes vs last commit
git diff --staged        # staged changes that will be committed
```

Shows the actual line-by-line changes. Use it before committing to review exactly
what you're about to save.

### `git show`

```bash
git show 54e796b
```

Displays the full details and diff of a single commit by its hash.

---

## 8. Undoing Things (use with care)

### `git restore`

```bash
git restore index.html        # discard unsaved edits to a file
git restore --staged index.html   # unstage a file (keep the edits)
```

Reverts a file in your working folder back to the last committed version, or removes
it from staging. The first form **deletes uncommitted edits** to that file — make
sure you don't need them.

### `git reset`

```bash
git reset --soft HEAD~1     # undo last commit, keep changes staged
git reset HEAD~1            # undo last commit, keep changes unstaged
git reset --hard HEAD~1     # undo last commit AND discard its changes
```

Moves the branch back by one or more commits. `--soft` and the default keep your
work; `--hard` throws it away. **`--hard` is destructive** — only use it when you're
sure you don't need those changes.

### `git revert`

```bash
git revert <commit-hash>
```

Creates a **new** commit that undoes an earlier one. Unlike `reset`, it doesn't
rewrite history, so it's the safe choice for undoing something that was already
pushed to GitHub.

---

## 9. Branches

### `git branch`

```bash
git branch                  # list branches
git branch --show-current   # show the branch you're on
git branch new-feature      # create a branch
git branch -d new-feature   # delete a merged branch
```

Branches let you work on something without disturbing `main`.

### `git switch` / `git checkout`

```bash
git switch new-feature        # move onto an existing branch
git switch -c new-feature     # create and move onto a new branch
git checkout new-feature      # older equivalent of switch
```

Changes which branch your working folder reflects. `git switch` is the newer, clearer
command; `git checkout` does the same and more but is easy to misuse.

### `git stash`

```bash
git stash        # set aside uncommitted changes temporarily
git stash pop    # bring them back
```

Tucks away work-in-progress so you can pull or switch branches with a clean folder,
then restores it afterward. The safest way to "hold that thought" without committing.

---

## 10. GitHub CLI (`gh`)

The `gh` tool talks to GitHub from the terminal — useful for things plain Git can't
do, like creating repos or checking your account.

### `gh auth status`

```bash
gh auth status
```

Shows which GitHub account you're logged in as and that authentication is working.

### `gh repo list` / `gh repo view`

```bash
gh repo list --limit 200          # list your repos
gh repo view user/repo            # details about one repo
```

Browse your repositories without opening a browser. We used this to check whether a
`technotes` repo already existed before pushing.

### `gh repo create`

```bash
gh repo create my-repo --public --source=. --remote=origin --push
```

Creates a new GitHub repo and (with these flags) wires it to your current local
folder and pushes in one step.

### `gh api`

```bash
gh api "repos/user/repo/git/trees/main?recursive=1" --jq '.tree[].path'
```

Calls the raw GitHub API for anything the friendlier commands don't cover — here,
listing every file in a repo. `--jq` filters the JSON response.

---

## Quick Reference Cheat Sheet

| Goal | Command |
|------|---------|
| Start a new repo | `git init` |
| Copy an existing repo | `git clone <url>` |
| Check current state | `git status` |
| Stage all changes | `git add .` |
| Save a snapshot | `git commit -m "message"` |
| Connect to GitHub | `git remote add origin <url>` |
| See remotes | `git remote -v` |
| Check GitHub for updates (safe) | `git fetch origin` |
| Download + apply updates | `git pull` |
| Upload commits | `git push` (first time: `git push -u origin main`) |
| Join unrelated repos | `git merge origin/main --allow-unrelated-histories` |
| See history | `git log --oneline` |
| See what changed | `git diff` |
| Set work aside | `git stash` / `git stash pop` |
| Undo last commit (keep work) | `git reset HEAD~1` |
| Undo a pushed commit safely | `git revert <hash>` |

---

## The Two Habits Worth Memorizing

1. **Pull before you start, push when you finish.**

   ```bash
   git pull              # before work
   # ...do work...
   git add .
   git commit -m "Describe what changed"
   git push              # after work
   ```

2. **When in doubt, run `git status`.** It never changes anything and always tells
   you where you stand.
