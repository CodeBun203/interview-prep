# Git and Version Control: Complete Professional Guide
## From Zero to FAANG Standard

---

## Table of Contents
1. What Git Actually Is
2. Installation and First-Time Setup
3. The Three Areas of Git
4. Your First Repository
5. Commits: The Core Unit of Git
6. Branching
7. Merging
8. Rebasing
9. Working with Remotes
10. The Pull Request Workflow
11. Professional Team Workflows
12. Resolving Merge Conflicts
13. Undoing Mistakes
14. Advanced Commands
15. Git Log and History Inspection
16. .gitignore
17. Commit Message Standards
18. Tagging and Releases
19. Git Hooks
20. Git Internals: How It Actually Works
21. FAANG-Level Best Practices

---

## 1. What Git Actually Is

Git is a distributed version control system. Every developer has a complete copy of the entire repository history on their machine, not just the current state of files.

**What this means practically:**
- You can commit, branch, merge, and view history with no internet connection
- If the central server disappears, every developer's clone is a full backup
- Operations are fast because they happen locally

**What Git is NOT:**
- A backup system (though it can be used as one)
- A deployment system (though pipelines use it)
- GitHub (GitHub is a hosting service built on top of Git)

**The mental model that matters:**
Git tracks a graph of snapshots. Each commit is a snapshot of the entire project at a point in time, plus a pointer to its parent commit(s). A branch is just a lightweight pointer to a commit. That is the whole data model.

```
A <- B <- C <- D   (main)
              |
              E <- F   (feature-branch)
```

---

## 2. Installation and First-Time Setup

### Install Git

```bash
# macOS (via Homebrew)
brew install git

# Ubuntu/Debian
sudo apt install git

# Windows
# Download from git-scm.com or use Git for Windows
winget install Git.Git
```

### First-time configuration

These settings go into `~/.gitconfig` and apply to every repository on your machine.

```bash
# Your identity (appears in every commit you make)
git config --global user.name "Hayden NeSmith"
git config --global user.email "nesmithhayden@gmail.com"

# Default branch name (industry standard is now 'main')
git config --global init.defaultBranch main

# Default editor for commit messages
git config --global core.editor "code --wait"   # VS Code
git config --global core.editor "vim"           # Vim

# Better diff output
git config --global core.pager "less -FRX"

# Auto-correct typos (waits 5 tenths of a second before running)
git config --global help.autocorrect 5

# Store credentials (so you do not type your password every push)
git config --global credential.helper store       # permanent
git config --global credential.helper cache       # temporary (15 min)

# Line ending handling
git config --global core.autocrlf input    # macOS/Linux
git config --global core.autocrlf true     # Windows

# Rebase by default when pulling (better than merge commits on pull)
git config --global pull.rebase true
```

**View your configuration:**
```bash
git config --list              # show all settings
git config user.name           # show one setting
git config --list --show-origin  # show where each setting comes from
```

---

## 3. The Three Areas of Git

This is the single most important concept to understand. Almost every Git operation is about moving changes between these three areas.

```
Working Directory  →  Staging Area (Index)  →  Repository (.git)
   (your files)         (git add)              (git commit)
```

**Working Directory:** The files you see and edit in your project folder. Git knows about them but does not track changes to them until you explicitly tell it to.

**Staging Area (also called the Index):** A preparation area where you build up the next commit. You choose exactly which changes go into a commit by staging them. This is what makes Git powerful: you can make five changes and commit them as three separate, logical commits.

**Repository:** The `.git` folder inside your project. This is where all commits, branches, and the full history live. When you commit, the staged changes become permanent.

```bash
# See what is in each area
git status                    # shows working directory and staging area state

# Move from working directory to staging area
git add filename.py           # stage one file
git add src/                  # stage a directory
git add -p                    # stage interactively, hunk by hunk (IMPORTANT)
git add .                     # stage everything (use carefully)

# Move from staging area to repository
git commit -m "message"       # commit with inline message
git commit                    # opens editor for commit message

# Move from repository back to staging area
git reset --soft HEAD~1       # undo last commit, keep changes staged

# Move from repository back to working directory
git reset HEAD~1              # undo last commit, keep changes unstaged

# Discard working directory changes (DANGEROUS: cannot be undone)
git checkout -- filename.py   # discard changes to one file
git restore filename.py       # modern syntax (Git 2.23+)
```

---

## 4. Your First Repository

### Starting from scratch

```bash
mkdir my-project
cd my-project
git init                      # creates .git folder
```

### Cloning an existing repository

```bash
git clone https://github.com/user/repo.git        # clone into repo/ folder
git clone https://github.com/user/repo.git mydir  # clone into mydir/
git clone --depth 1 https://github.com/user/repo.git  # shallow clone (no history)
```

### The basic daily loop

```bash
# 1. Check what changed
git status

# 2. See the actual changes
git diff                      # unstaged changes
git diff --staged             # staged changes

# 3. Stage what you want to commit
git add -p                    # review and stage hunks interactively

# 4. Commit
git commit -m "feat: add subscription renewal endpoint"

# 5. Push
git push
```

---

## 5. Commits: The Core Unit of Git

A commit records:
- A snapshot of all tracked files
- Author name and email
- Timestamp
- Your commit message
- A pointer to the parent commit(s)
- A unique SHA-1 hash (like `a3f8b2c`)

### What makes a good commit

**Atomic:** Each commit should contain one logical change. Not "added feature and fixed three bugs." If you worked on three things, commit them separately.

**Complete:** The project should build and tests should pass at every commit. This is critical because `git bisect` (finding which commit introduced a bug) only works if intermediate commits are functional.

**Descriptive:** The message should explain why the change was made, not what was changed. The diff shows what changed.

### Viewing commits

```bash
git log                        # full log
git log --oneline              # compact: one line per commit
git log --oneline --graph      # with branch diagram
git log --oneline -10          # last 10 commits
git log --author="Hayden"      # filter by author
git log --since="2 weeks ago"  # filter by date
git log -- filename.py         # commits that touched a specific file
git show a3f8b2c               # show a specific commit's full diff
git show HEAD                  # show the latest commit
git show HEAD~2                # show the commit 2 before HEAD
```

---

## 6. Branching

A branch is a pointer to a commit. Creating a branch costs nothing and takes microseconds. Use branches constantly.

### The HEAD pointer

`HEAD` is a special pointer that tells Git which branch you are currently on. When you commit, the current branch moves forward to the new commit, and HEAD moves with it.

```
main:    A <- B <- C <- D      (HEAD -> main)
```

After `git checkout -b feature`:
```
main:    A <- B <- C <- D
                         ^
feature:              (HEAD -> feature)
```

After a commit on feature:
```
main:    A <- B <- C <- D
                         \
feature:                  E     (HEAD -> feature)
```

### Branch commands

```bash
# Creating branches
git branch feature-login       # create branch at current position
git checkout -b feature-login  # create and switch to it
git switch -c feature-login    # modern syntax (Git 2.23+)

# Switching branches
git checkout main              # switch to main
git switch main                # modern syntax

# Listing branches
git branch                     # local branches
git branch -r                  # remote branches
git branch -a                  # all branches
git branch -v                  # with last commit info

# Renaming
git branch -m old-name new-name

# Deleting
git branch -d feature-login    # delete (safe: refuses if unmerged)
git branch -D feature-login    # force delete

# See which branches are merged into current
git branch --merged
git branch --no-merged
```

### Detached HEAD state

If you checkout a commit directly (not a branch), you are in "detached HEAD" state. Any commits you make here are not on any branch and will be garbage collected unless you create a branch.

```bash
git checkout a3f8b2c           # detached HEAD

# If you made commits in detached HEAD and want to keep them:
git checkout -b recovery-branch   # create a branch at current position
```

---

## 7. Merging

Merging combines the history of two branches.

### Fast-forward merge

If the target branch has not diverged from the source, Git just moves the pointer forward. No merge commit is created.

```
Before:
main:    A <- B <- C
                    ^
feature:             D <- E    (HEAD -> feature)

After: git checkout main && git merge feature
main:    A <- B <- C <- D <- E    (HEAD -> main)
```

```bash
git checkout main
git merge feature-login        # fast-forward if possible
git merge --ff-only feature    # fail if not fast-forwardable
git merge --no-ff feature      # always create a merge commit
```

### Three-way merge

When both branches have diverged, Git creates a merge commit with two parents.

```
Before:
main:    A <- B <- C <- F
                    \
feature:             D <- E

After: git merge feature
main:    A <- B <- C <- F <- M     (M is the merge commit, has two parents: F and E)
                    \       /
feature:             D <- E
```

### Merge commit message

When a merge commit is created, Git opens your editor with a default message. For a clean history, you can customize it:
```
Merge feature/subscription-renewal into main

Adds the subscription renewal batch processor with:
- Exponential backoff on payment failures
- Prometheus metrics for renewal rate tracking
- 95% test coverage
```

---

## 8. Rebasing

Rebasing rewrites history by replaying commits on top of a new base. It produces a linear history instead of a branching one.

```
Before:
main:    A <- B <- C <- F
                    \
feature:             D <- E

After: git checkout feature && git rebase main
main:    A <- B <- C <- F
                          \
feature:                   D' <- E'   (D and E replayed on top of F)
```

D' and E' are new commits with new SHAs. The original D and E still exist but are no longer referenced by any branch.

### Basic rebase

```bash
git checkout feature-login
git rebase main                # rebase feature onto current tip of main
```

### Interactive rebase

The most powerful Git command for professional work. Lets you rewrite, squash, reorder, and edit commits before sharing them.

```bash
git rebase -i HEAD~4           # interactively edit the last 4 commits
git rebase -i main             # interactively edit all commits since branching from main
```

This opens an editor showing the commits:
```
pick a3f8b2c feat: add subscription model
pick b7e1d4f wip: half-done renewal logic
pick c9a2f8e fix typo
pick d1b3e7a feat: complete renewal endpoint

# Commands:
# p, pick   = use commit as-is
# r, reword = use commit, but edit the message
# e, edit   = use commit, but stop to amend
# s, squash = combine into previous commit
# f, fixup  = like squash but discard this commit's message
# d, drop   = remove this commit entirely
```

**Common interactive rebase operations:**

Squash work-in-progress commits into one clean commit:
```
pick a3f8b2c feat: add subscription model
squash b7e1d4f wip: half-done renewal logic
squash c9a2f8e fix typo
reword d1b3e7a feat: complete renewal endpoint
```

This produces two commits: the subscription model and the completed renewal endpoint.

### The Golden Rule of Rebasing

**Never rebase commits that have been pushed to a shared remote branch.**

Rebase rewrites history (new SHAs). If your teammates have already pulled those commits, their history diverges from yours. The result is a confusing mess. Rebase is for cleaning up your local work before pushing, not for rewriting shared history.

Safe: `git rebase main` on a feature branch only you are using.
Dangerous: `git rebase` on `main` after you have pushed `main` and teammates have pulled it.

### Merge vs Rebase: When to use each

| Situation | Use |
|---|---|
| Bringing a feature branch up to date with main | `rebase` |
| Merging a completed feature into main | `merge --no-ff` (preserves the branch in history) |
| Cleaning up messy commits before a PR | `rebase -i` |
| Merging a colleague's branch you cannot rewrite | `merge` |
| Public/shared branches | Never rebase |

---

## 9. Working with Remotes

A remote is a version of your repository hosted elsewhere (GitHub, GitLab, Bitbucket, internal server).

### Remote commands

```bash
# View remotes
git remote -v                              # show remote URLs

# Add a remote
git remote add origin https://github.com/user/repo.git
git remote add upstream https://github.com/original/repo.git  # for forks

# Rename/remove
git remote rename origin new-name
git remote remove upstream

# Fetch: download remote changes WITHOUT merging them
git fetch origin                           # fetch all branches from origin
git fetch origin main                      # fetch just main
git fetch --all                            # fetch all remotes

# Pull: fetch AND merge (or rebase if configured)
git pull                                   # pull current branch
git pull origin main                       # pull specific branch
git pull --rebase                          # pull with rebase instead of merge

# Push
git push origin feature-login             # push a branch
git push -u origin feature-login          # push and set upstream tracking
git push                                  # push current branch (if tracking set)
git push --tags                           # push tags

# Delete remote branch
git push origin --delete feature-login
```

### Fetch vs Pull

`git fetch` downloads changes but does not touch your working directory or branches. It is always safe. Use it to see what changed on the remote before deciding what to do.

`git pull` is `git fetch` followed by `git merge` (or `git rebase` if configured). It directly affects your current branch.

```bash
# Best practice: fetch first, inspect, then merge/rebase
git fetch origin
git log HEAD..origin/main --oneline    # see what is new on remote
git merge origin/main                  # or: git rebase origin/main
```

### Tracking branches

When you push with `-u`, Git sets up tracking so `git push` and `git pull` know which remote branch to use.

```bash
git push -u origin feature-login
# Now just:
git push
git pull
```

---

## 10. The Pull Request Workflow

Pull requests (PRs) are not a Git feature. They are a GitHub/GitLab/Bitbucket feature built on top of Git. They provide a structured way to propose and review changes before merging into a shared branch.

### The full professional PR workflow

**Step 1: Create a feature branch from main**
```bash
git checkout main
git pull                           # make sure you are up to date
git checkout -b feat/subscription-renewal
```

Branch naming conventions:
```
feat/short-description             # new feature
fix/short-description              # bug fix
chore/short-description            # maintenance, no behavior change
docs/short-description             # documentation only
refactor/short-description         # refactoring
test/short-description             # tests only
```

**Step 2: Work in commits**
```bash
# Make changes, then stage and commit
git add -p                         # review changes interactively
git commit -m "feat: add Subscription model with billing fields"

# More changes
git add -p
git commit -m "feat: add renewal batch processor"

# More changes
git add -p
git commit -m "test: add 95% coverage for renewal processor"
```

**Step 3: Keep your branch up to date**
```bash
git fetch origin
git rebase origin/main             # replay your commits on top of latest main
# If conflicts: resolve them, then git rebase --continue
```

**Step 4: Clean up your commits before pushing (optional but professional)**
```bash
git rebase -i origin/main          # squash WIP commits, fix messages
```

**Step 5: Push and open the PR**
```bash
git push -u origin feat/subscription-renewal
# Then open GitHub and create the PR
```

**Step 6: Respond to review feedback**
```bash
# Make changes based on reviewer feedback
git add -p
git commit -m "fix: address review feedback on error handling"
git push                           # push updates to the same PR
```

**Step 7: Merge**
After approval, the PR is merged. Most companies use one of:
- **Squash and merge:** All PR commits become one commit on main (clean history)
- **Rebase and merge:** All PR commits replayed onto main (linear history)
- **Merge commit:** A merge commit is created (preserves branch structure)

**Step 8: Clean up**
```bash
git checkout main
git pull                           # get the merged commit
git branch -d feat/subscription-renewal  # delete local branch
```

### Writing a good PR description

A PR description is documentation. Future engineers will read it when trying to understand why code changed.

```markdown
## What

Adds a subscription renewal batch processor that runs nightly to attempt
payment for all subscriptions due today.

## Why

Manual renewal processing was taking 3+ hours of engineer time per week
and had a 5% failure rate due to missed subscriptions. This automates
the process and handles retry logic.

## How

- Added RenewalBatchProcessor class in app/services/renewal.py
- Uses exponential backoff (max 3 retries) for declined payments
- Subscriptions that fail after all retries are marked past_due, not cancelled
- Prometheus counter added for renewal success/failure rates

## Testing

- Unit tests: 95% coverage on processor logic
- Integration test: end-to-end flow with test Stripe sandbox
- Manual test: ran against staging with 50 subscriptions, all processed correctly

## Risks

- First run will process all subscriptions that missed yesterday's window
  due to the migration. Run with --dry-run first to verify count.
```

---

## 11. Professional Team Workflows

### GitHub Flow (recommended for most teams)

Simple. One long-lived branch (main). Everything else is short-lived feature branches.

```
main ─────────────────────────────────────────────────────► (always deployable)
       \             /    \                    /
        feature-a──/      feature-b──────────/
```

Rules:
- `main` is always deployable
- All work happens in branches
- Branches are short-lived (days, not weeks)
- PR review required before merging to main
- Deploy immediately after merging to main

### Trunk-Based Development (used at Google, Facebook, Amazon)

Even simpler. Developers commit directly to `main` (the "trunk") multiple times per day. Feature flags hide incomplete features.

```
main: A─B─C─D─E─F─G─H─I─J   (everyone commits here)
```

What makes this work:
- Very small commits (hours of work, not days)
- Feature flags: `if feature_flags.get("new_checkout"): ...`
- Strong CI that runs in minutes
- Engineers monitor deployments they trigger

### GitFlow (legacy, still used at some companies)

More complex. Multiple long-lived branches.

```
main:    ────────●────────────────────●──────────────
                 │                    │
hotfix:          └──fix─┐             │
                         │            │
release:                 └────────────●
                                      │
develop: ──●──────────────────────────●──────────────
            \          /
             feature──/
```

Branches:
- `main`: production releases only
- `develop`: integration branch, always has latest development
- `feature/*`: new features, branch from develop
- `release/*`: release preparation, branch from develop
- `hotfix/*`: emergency fixes, branch from main

This is considered heavyweight by modern standards. Most teams have moved to GitHub Flow or trunk-based.

### Which workflow you will likely use

- **Amazon:** Trunk-based development with feature flags. Short-lived branches or direct commits to main.
- **Apple:** GitHub Flow variant. PRs required, strict review process.
- **Snowflake:** GitHub Flow. PRs with multiple reviewers for significant changes.

---

## 12. Resolving Merge Conflicts

A conflict occurs when two branches modify the same lines of a file and Git cannot automatically determine which change should win.

### What a conflict looks like

```python
<<<<<<< HEAD
def calculate_total(items, discount=0):
    return sum(item.price for item in items) * (1 - discount)
=======
def calculate_total(items, tax_rate=0.1):
    subtotal = sum(item.price for item in items)
    return subtotal + (subtotal * tax_rate)
>>>>>>> feature/add-tax-calculation
```

- Everything between `<<<<<<< HEAD` and `=======` is your version
- Everything between `=======` and `>>>>>>> branch-name` is the incoming version

### Resolving conflicts

```bash
# See which files have conflicts
git status

# Open each conflicted file and resolve manually
# Remove the conflict markers and write the correct merged code:

def calculate_total(items, discount=0, tax_rate=0.0):
    subtotal = sum(item.price for item in items) * (1 - discount)
    return subtotal + (subtotal * tax_rate)

# After resolving:
git add filename.py            # mark as resolved
git commit                     # complete the merge
# OR if you were rebasing:
git rebase --continue
```

### Using a merge tool

```bash
git mergetool                  # opens your configured merge tool
git config --global merge.tool vscode

# VS Code settings for mergetool:
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

### Aborting a conflict

```bash
git merge --abort              # abandon the merge, return to pre-merge state
git rebase --abort             # abandon the rebase
```

### Preventing conflicts

- Keep branches short-lived (days, not weeks)
- Rebase your branch onto main regularly
- Communicate with teammates when working in the same files
- Break large files into smaller focused modules

---

## 13. Undoing Mistakes

This section will save you from panic at 2am.

### The safety principle

Git almost never permanently deletes anything. Even "deleted" commits are recoverable for ~30 days via the reflog. Do not panic.

### Amending the last commit

```bash
# Fix the commit message
git commit --amend -m "better message"

# Add a forgotten file to the last commit
git add forgotten_file.py
git commit --amend --no-edit          # keep the same message

# ONLY safe if you have NOT pushed this commit yet
```

### Undoing commits (keeping changes)

```bash
git reset --soft HEAD~1        # undo last commit, keep changes STAGED
git reset HEAD~1               # undo last commit, keep changes UNSTAGED (default)
git reset HEAD~3               # undo last 3 commits, keep all changes unstaged
```

### Undoing commits (discarding changes)

```bash
git reset --hard HEAD~1        # DANGER: undo last commit AND discard changes
git reset --hard origin/main   # DANGER: discard all local commits, match remote
```

`--hard` discards changes. Do not use it unless you are certain you do not want them.

### Reverting (safe for shared branches)

`git revert` creates a new commit that undoes a previous commit. This is safe because it does not rewrite history.

```bash
git revert HEAD                # create a revert of the last commit
git revert a3f8b2c             # create a revert of a specific commit
git revert HEAD~3..HEAD        # revert the last 3 commits
```

Use `revert` on shared branches (main, develop). Use `reset` only on your own local branches.

### Recovering from the reflog

The reflog is a local log of everywhere HEAD has pointed. It records every checkout, commit, reset, and rebase.

```bash
git reflog                     # show reflog

# Output:
# a3f8b2c HEAD@{0}: commit: feat: add renewal processor
# b7e1d4f HEAD@{1}: reset: moving to HEAD~1
# c9a2f8e HEAD@{2}: commit: wip: half done

# Recover a "lost" commit
git checkout HEAD@{2}          # go to that state
git checkout -b recovery       # save it as a branch
```

The reflog is your safety net. If you panic after a bad reset, check the reflog first.

### Unstaging files

```bash
git restore --staged filename.py    # unstage a file (Git 2.23+)
git reset HEAD filename.py          # older syntax
```

### Discarding working directory changes

```bash
git restore filename.py        # discard changes to one file (Git 2.23+)
git checkout -- filename.py    # older syntax
git restore .                  # discard ALL working directory changes (DANGER)
```

### Stashing changes

Stash is a temporary shelf for changes you are not ready to commit but need to set aside.

```bash
git stash                      # stash all changes
git stash push -m "half-done auth work"  # stash with a message
git stash list                 # see all stashes
git stash pop                  # apply most recent stash and remove it
git stash apply stash@{2}      # apply a specific stash, keep it in list
git stash drop stash@{2}       # delete a specific stash
git stash clear                # delete all stashes
git stash branch feature/from-stash  # create a branch from a stash
```

**When to use stash:**
- You are in the middle of work and need to pull the latest main
- A coworker asks you to quickly review something and you have uncommitted work
- You want to test something on a clean slate without committing your work-in-progress

---

## 14. Advanced Commands

### cherry-pick

Apply a specific commit from one branch onto another. Useful for applying a hotfix to multiple branches.

```bash
git cherry-pick a3f8b2c               # apply one commit
git cherry-pick a3f8b2c b7e1d4f       # apply multiple commits
git cherry-pick main~3..main          # apply a range of commits
git cherry-pick --no-commit a3f8b2c   # apply changes but do not commit
```

### bisect

Binary search through history to find which commit introduced a bug. Indispensable for debugging regressions.

```bash
git bisect start
git bisect bad                 # current commit is broken
git bisect good v2.0.0         # this tag was working

# Git checks out a commit in the middle of the range
# Test whether the bug exists, then:
git bisect good                # this commit is fine, bug is later
# OR
git bisect bad                 # this commit has the bug, it is earlier

# Git narrows down until it identifies the exact commit
# When done:
git bisect reset               # return to HEAD
```

For large histories, bisect finds the offending commit in O(log n) steps instead of checking every commit linearly.

### blame

Show who last modified each line of a file and in which commit.

```bash
git blame filename.py              # annotate entire file
git blame -L 10,25 filename.py     # only lines 10-25
git blame --follow filename.py     # follow renames
```

Useful for understanding why a line of code exists. Find the commit, then read the PR description for full context.

### diff advanced usage

```bash
git diff main..feature             # all changes between two branches
git diff HEAD~3                    # changes since 3 commits ago
git diff --stat                    # summary: files changed, insertions, deletions
git diff --word-diff               # show changes at word level, not line level
git diff --ignore-whitespace       # ignore whitespace-only changes
git diff main -- src/api/          # diff only files in a specific directory
```

### clean

Remove untracked files from your working directory.

```bash
git clean -n                   # dry run: show what would be deleted
git clean -f                   # delete untracked files
git clean -fd                  # delete untracked files AND directories
git clean -fX                  # delete only files listed in .gitignore
```

---

## 15. Git Log and History Inspection

```bash
# Basic formats
git log                                    # full log
git log --oneline                          # compact
git log --oneline --graph --all            # visual branch diagram

# Filtering
git log --author="Hayden"                  # by author
git log --since="2024-01-01"               # after a date
git log --until="2024-06-01"               # before a date
git log --grep="subscription"              # commits matching a keyword
git log -S "calculate_total"               # commits that added/removed this string
git log -- src/api/orders.py               # commits touching a specific file

# Formats
git log --format="%h %an %s"               # custom format
git log --format="%h %ad %s" --date=short  # with date

# Useful aliases to add to ~/.gitconfig
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.last "log -1 HEAD"
git config --global alias.s "status -sb"
```

### Finding when something was introduced

```bash
# Find the commit that added a specific string
git log -S "PaymentDeclinedError" --source --all

# Find the commit that changed a specific function
git log -L :calculate_total:src/orders.py

# Show full diff of a specific commit
git show a3f8b2c

# Show all files changed in a commit
git show --name-only a3f8b2c
git show --stat a3f8b2c
```

---

## 16. .gitignore

`.gitignore` tells Git which files to never track. Put it in your repository root and commit it.

### Syntax

```gitignore
# Comments start with #

# Ignore a specific file
secrets.env

# Ignore all files with an extension
*.pyc
*.log
*.tmp

# Ignore a directory (trailing slash is convention but not required)
node_modules/
__pycache__/
.venv/
dist/
build/

# Ignore a pattern in any subdirectory
**/*.pyc
**/node_modules/

# Ignore a specific file in a specific directory
src/config/local.py

# Exception: track this file even though the pattern above ignores it
!src/config/example.py

# Ignore all files in a directory but keep the directory itself
logs/*
!logs/.gitkeep     # .gitkeep is an empty placeholder file
```

### Standard .gitignore for a Python project

```gitignore
# Python
__pycache__/
*.py[cod]
*.pyo
*.pyd
.Python
*.egg-info/
dist/
build/
.eggs/

# Virtual environments
.venv/
venv/
env/
ENV/

# Testing
.pytest_cache/
.coverage
htmlcov/
.tox/

# IDE
.vscode/
.idea/
*.swp
*.swo
.DS_Store

# Environment variables and secrets
.env
.env.local
.env.*.local
secrets.json
credentials.json
*.key
*.pem

# Logs
*.log
logs/

# Distribution
*.tar.gz
*.zip
```

### .gitignore vs .gitignore_global

Local `.gitignore` (in your repo): tracks project-specific ignores. Commit this.

Global `~/.gitignore_global`: tracks personal ignores that apply to every repo (OS files, editor temp files).

```bash
git config --global core.excludesfile ~/.gitignore_global
```

```gitignore
# ~/.gitignore_global
.DS_Store
Thumbs.db
.idea/
*.swp
```

### Untracking a file you accidentally committed

```bash
# Remove from tracking but keep the file locally
git rm --cached filename.py
git rm --cached -r directory/    # for a directory

# Then add to .gitignore and commit
echo "filename.py" >> .gitignore
git add .gitignore
git commit -m "chore: stop tracking filename.py"
```

---

## 17. Commit Message Standards

Professional teams use a standard commit message format. The most widely adopted is **Conventional Commits**.

### Conventional Commits format

```
<type>[optional scope]: <description>

[optional body]

[optional footer]
```

**Types:**
| Type | Use for |
|---|---|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation changes only |
| `style` | Formatting, no logic change |
| `refactor` | Code change that is not a fix or feature |
| `test` | Adding or modifying tests |
| `chore` | Build process, dependency updates, tooling |
| `perf` | Performance improvement |
| `ci` | CI/CD configuration changes |
| `revert` | Reverts a previous commit |

**Examples:**
```
feat: add subscription renewal batch processor

fix: prevent duplicate lead creation on retry

docs: add API authentication guide to README

refactor: extract payment validation into separate service

test: add edge case coverage for zero-quantity orders

chore: upgrade SQLAlchemy from 1.4 to 2.0

feat(auth): add JWT refresh token rotation

fix(api): return 422 instead of 500 for invalid subscription plan codes

feat!: require API key authentication on all endpoints

BREAKING CHANGE: All API endpoints now require Bearer token authentication.
Clients must include Authorization: Bearer <token> header.
```

**The `!` suffix** indicates a breaking change.

### The 7 rules of a great commit message

1. Separate subject from body with a blank line
2. Limit the subject line to 72 characters
3. Do not end the subject with a period
4. Use the imperative mood: "add feature" not "added feature"
5. Use the body to explain what and why, not how
6. Wrap the body at 72 characters
7. Reference issues and PRs in the footer

**Full example:**
```
feat(subscriptions): add renewal batch processor with retry logic

Automates the nightly subscription renewal cycle that was previously
done manually. Manual processing took 3+ hours/week and had a 5%
error rate due to missed subscriptions.

The processor handles payment failures with exponential backoff
(3 retries) and marks subscriptions as past_due rather than
immediately cancelling them. This gives customers time to update
their payment method.

Closes #234
Related: #198, #201
```

---

## 18. Tagging and Releases

Tags are like branches that never move. They permanently mark a specific commit as meaningful, typically a release.

### Creating tags

```bash
# Lightweight tag (just a pointer, no extra information)
git tag v1.0.0

# Annotated tag (recommended: includes tagger, date, message)
git tag -a v1.0.0 -m "Version 1.0.0: initial production release"

# Tag a specific past commit
git tag -a v0.9.0 a3f8b2c -m "Version 0.9.0 beta"

# Push tags to remote (tags are not pushed with normal git push)
git push origin v1.0.0
git push origin --tags             # push all tags
```

### Viewing and using tags

```bash
git tag                            # list all tags
git tag -l "v1.*"                  # list tags matching a pattern
git show v1.0.0                    # show tag details and commit

git checkout v1.0.0                # check out the code at a tag (detached HEAD)
git checkout -b hotfix/v1.0.1 v1.0.0  # create a branch from a tag
```

### Semantic versioning (SemVer)

The standard for version numbers: `MAJOR.MINOR.PATCH`

- **MAJOR:** Increments when you make incompatible API changes (`2.0.0`)
- **MINOR:** Increments when you add functionality in a backward-compatible way (`1.1.0`)
- **PATCH:** Increments when you make backward-compatible bug fixes (`1.0.1`)

Pre-release: `1.0.0-alpha.1`, `1.0.0-beta.2`, `1.0.0-rc.1`

---

## 19. Git Hooks

Hooks are scripts that run automatically at specific points in Git workflows. They live in `.git/hooks/` but you can use a tool like `pre-commit` to manage them in your repository.

### Common hooks

**pre-commit:** Runs before a commit is created. Use it to run linters, formatters, and quick tests. If it exits non-zero, the commit is aborted.

**commit-msg:** Runs after you type your commit message but before the commit is finalized. Use it to enforce commit message format.

**pre-push:** Runs before `git push`. Use it to run your full test suite.

### Setting up hooks with pre-commit (the professional way)

```bash
pip install pre-commit
```

Create `.pre-commit-config.yaml` in your project root:

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.4.4
    hooks:
      - id: ruff                    # Python linter
      - id: ruff-format             # Python formatter

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: trailing-whitespace     # remove trailing whitespace
      - id: end-of-file-fixer       # ensure files end with newline
      - id: check-yaml              # validate YAML syntax
      - id: check-json              # validate JSON syntax
      - id: check-merge-conflict    # catch leftover conflict markers
      - id: detect-private-key      # prevent committing private keys
      - id: no-commit-to-branch     # prevent direct commits to main
        args: ['--branch', 'main']

  - repo: local
    hooks:
      - id: pytest-unit
        name: Run unit tests
        entry: pytest tests/unit/ -x -q
        language: system
        pass_filenames: false
        stages: [pre-push]          # only run on push, not every commit
```

```bash
pre-commit install                  # install the git hooks
pre-commit install --hook-type commit-msg  # also install commit-msg hook
pre-commit run --all-files          # run manually on all files
```

### Enforcing commit message format

```yaml
  - repo: https://github.com/compilerla/conventional-pre-commit
    rev: v3.2.0
    hooks:
      - id: conventional-pre-commit
        stages: [commit-msg]
```

---

## 20. Git Internals: How It Actually Works

Understanding this turns Git from a collection of commands into a coherent system.

### The object store

Everything Git stores is one of four object types, all identified by SHA-1 hash:

- **blob:** The contents of a file (no filename, no metadata)
- **tree:** A directory listing: maps filenames to blobs and other trees
- **commit:** Points to a tree (the root of the project snapshot), parent commit(s), author, message
- **tag:** Annotated tag, points to a commit

```
commit a3f8b2c
├── tree b7e1d4f
│   ├── blob c9a2f8e  (README.md contents)
│   ├── blob d1b3e7a  (main.py contents)
│   └── tree e5c4f1b  (src/ directory)
│       ├── blob f8d2a3c  (src/orders.py contents)
│       └── blob g9e5b2d  (src/users.py contents)
└── parent 9f1e3c2    (points to previous commit)
```

This is why Git is so efficient at storage: if only one file changes, only that blob is new. All unchanged blobs are shared between commits.

### What a branch really is

```bash
# A branch is just a file containing a SHA-1 hash
cat .git/refs/heads/main
# a3f8b2c983f1e2d4c5b6a7d8e9f0c1b2a3d4e5f6

# HEAD is a file pointing to the current branch
cat .git/HEAD
# ref: refs/heads/main
```

When you commit, Git:
1. Creates blob objects for changed files
2. Creates tree objects for changed directories
3. Creates a commit object pointing to the root tree and parent commit
4. Updates the current branch file to point to the new commit

This is why branching is free and instant: it is just creating a new file with a hash.

### What happens during a rebase

Rebase does NOT move commits. It creates new commits with the same changes but different parent pointers (and therefore different SHAs). The original commits still exist in the object store until garbage collection removes unreferenced objects.

This is why the golden rule matters: if someone else has pulled your original commits, they have references to the old SHAs. After you rebase and force-push new SHAs, their history no longer matches yours.

---

## 21. FAANG-Level Best Practices

### Make every commit buildable and testable

Every commit in your history should leave the project in a working state. This enables:
- `git bisect` to work correctly
- Teammates to checkout any commit safely
- CI to run on any commit

### Never commit directly to main

Always use a branch. Even for one-line fixes. This enables:
- Code review before it reaches the shared branch
- CI validation before merging
- Easy revert if the change breaks something

### Pull request hygiene

- Keep PRs small. Under 400 lines changed is a reasonable target.
- One logical change per PR. A PR that adds a feature and refactors three unrelated modules is two PRs.
- Write a description that explains why, not just what.
- Respond to review comments within a day.
- Do not merge your own PR without review (except for emergencies).

### Keep branches short-lived

Branches that live longer than a week accumulate merge conflicts and diverge from main. The longer a branch lives, the harder it is to merge. Aim for branches that are opened and merged in 1-3 days.

### Write meaningful commit messages

Commit messages are the primary documentation for why code changed. Six months from now, `git blame` will point someone to your commit. Make the message explain why the change was necessary.

### Use `git add -p` always

Staging interactively forces you to review every change before it goes into a commit. This catches:
- Debug code left in (print statements, console.log)
- Commented-out code you meant to delete
- Changes that belong in a separate commit
- Accidental edits

### Force push carefully and never to main

```bash
# Safe force push: only force pushes your own new commits, refuses if
# someone else pushed to the branch since your last fetch
git push --force-with-lease
```

`--force-with-lease` is the professional alternative to `--force`. It refuses to push if the remote has commits you have not fetched, preventing accidental overwrites of a teammate's work.

Never force push to `main` or any shared branch. At most companies this is blocked at the repository level.

### The .git/config file

Each repository has its own local config in `.git/config`. Settings here override your global config for that repository.

```bash
git config user.email "work@company.com"    # use work email just for this repo
git config --local user.email "work@company.com"  # --local is the default
```

### Useful global aliases

```bash
git config --global alias.st "status -sb"
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.undo "reset HEAD~1"
git config --global alias.staged "diff --staged"
git config --global alias.aliases "config --get-regexp alias"
git config --global alias.pushf "push --force-with-lease"
```

---

## Complete Command Reference

### Daily workflow
```bash
git status                     # what changed
git diff                       # see unstaged changes
git diff --staged              # see staged changes
git add -p                     # stage interactively
git commit -m "type: message"  # commit
git push                       # push to remote
git pull --rebase              # pull with rebase
```

### Branching
```bash
git switch -c branch-name      # create and switch
git switch branch-name         # switch to existing
git branch -d branch-name      # delete merged branch
git branch -D branch-name      # force delete
git merge --no-ff branch       # merge with commit
git rebase main                # rebase onto main
git rebase -i HEAD~3           # interactive rebase last 3
```

### Undoing
```bash
git restore filename           # discard working dir changes
git restore --staged filename  # unstage
git commit --amend             # fix last commit (unpushed only)
git reset HEAD~1               # undo last commit, keep changes
git revert HEAD                # undo last commit safely (for shared branches)
git stash                      # shelf changes temporarily
git stash pop                  # restore shelved changes
git reflog                     # find lost commits
```

### Inspection
```bash
git log --oneline --graph      # visual history
git show commit-hash           # see a commit's changes
git blame filename             # who wrote each line
git diff branch1..branch2      # compare branches
git log -S "search term"       # find when a string was added
git bisect start               # start binary search for a bug
```

### Remote
```bash
git remote -v                  # list remotes
git fetch origin               # download without merging
git push -u origin branch      # push and set tracking
git push --force-with-lease    # safe force push
git push origin --delete name  # delete remote branch
```
