# Git and Version Control

## Overview

**Version Control** is the practice of tracking and managing changes to code. **Git** is the most widely used version control system in the world, powering everything from solo projects to the largest open-source repositories. Understanding Git deeply — not just the basic commands, but how it actually works — will save you hours of frustration and make you a more effective collaborator on any software team.

> **Key Insight**: Git is a **distributed** version control system. Every developer has a complete copy of the repository history, enabling offline work, faster operations, and multiple backup copies. Understanding Git's data model (commits, trees, blobs) makes even complex operations intuitive.

---

## What is Version Control?

Version control tracks changes to files over time, allowing you to:
- Revert to previous versions
- Compare changes over time
- Collaborate without overwriting each other's work
- Track who made what changes and why

### Centralized vs Distributed

```
Centralized (SVN, CVS)          Distributed (Git)

    ┌─────────┐                   ┌─────────┐
    │ Central │                   │ Repo A  │
    │  Repo   │←────────────────→│ Repo B  │
    └────┬────┘                   │ Repo C  │
         │                        └─────────┘
    ┌────┴────┐                   All are complete copies
    │  Users  │
    └─────────┘
    Each has only latest snapshot
```

---

## Git Fundamentals

### How Git Stores Data

Git doesn't store file differences. It stores **snapshots** of your entire file system.

```
Git Data Model:

┌──────────┐     ┌──────────┐     ┌──────────┐
│  Commit  │────→│  Tree    │────→│  Blob    │
│          │     │ (folder) │     │ (file)   │
│ - author │     │          │     │          │
│ - date   │     │ - name   │     │ - content│
│ - message│     │ - blobs  │     │          │
│ - parent │     │ - trees  │     │          │
└──────────┘     └──────────┘     └──────────┘
```

Every commit is identified by a **SHA-1 hash** of its contents.

```
Commit: a3f5d2e...
  ├─ tree: b8c1e4f...
  │    ├─ blob: "Hello World" (index.html)
  │    ├─ blob: "body { color: red }" (style.css)
  │    └─ tree: src/
  │         └─ blob: "console.log('hi')" (app.js)
  └─ parent: 9c2b1a0...
```

### The Three States

Git has three main states your files can be in:

```
Working Directory        Staging Area (Index)        Repository
┌──────────────┐         ┌──────────────┐           ┌──────────────┐
│  file.txt    │  git add│  file.txt    │  git commit│  file.txt    │
│  (modified)  │────────→│  (staged)    │──────────→│  (committed) │
└──────────────┘         └───────────��──┘           └──────────────┘
```

| State | Description |
|-------|-------------|
| **Working Directory** | Your actual files — modified but not tracked |
| **Staging Area (Index)** | Files marked for the next commit |
| **Repository** | Permanent snapshot of staged files |

---

## Essential Git Commands

### Setup and Configuration

```bash
# Set your identity
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Set default editor
git config --global core.editor "code --wait"

# View all config
git config --list

# View specific config
git config user.name
```

### Creating and Cloning Repositories

```bash
# Initialize a new repo
git init

# Clone an existing repo
git clone https://github.com/user/repo.git

# Clone with specific branch
git clone -b develop https://github.com/user/repo.git

# Clone into specific folder
git clone https://github.com/user/repo.git my-folder
```

### The Basic Workflow

```bash
# Check status
git status

# Stage files
git add file.txt              # Stage specific file
git add .                     # Stage all changes
git add -p                    # Stage interactively (patch mode)

# Unstage files
git restore --staged file.txt # Unstage (keep changes)
git restore file.txt          # Discard changes (dangerous!)

# Commit
git commit -m "Add login feature"
git commit -m "Title" -m "Detailed description"

# Stage and commit in one
git commit -am "Quick fix"    # Only works for tracked files
```

### Viewing History

```bash
# View commit history
git log
git log --oneline             # Compact view
git log --graph --oneline --all --decorate  # Visual branch view
git log -p                    # Show patch (diff)
git log --stat                # Show file stats
git log --author="Alice"      # Filter by author
git log --since="1 week ago"  # Filter by date

# View specific commit
git show abc1234

# View file history
git log -p -- file.txt
```

---

## Branching

Branches are lightweight pointers to commits. They're the foundation of parallel development.

```
main:    A---B---C---F---G
               \
feature:        D---E
```

### Branch Commands

```bash
# List branches
git branch                    # Local branches
git branch -a                 # All branches (including remote)
git branch -vv                # With tracking info

# Create branch
git branch feature-x          # Create only
git checkout -b feature-x     # Create and switch
git switch -c feature-x       # Modern syntax (Git 2.23+)

# Switch branches
git checkout main
git switch main               # Modern syntax

# Rename branch
git branch -m old-name new-name

# Delete branch
git branch -d feature-x       # Safe delete (merged)
git branch -D feature-x       # Force delete
```

### Branching Strategies

**Git Flow:**
```
main     ●────●────●────●────●────●
          \         /\         /
hotfix     ●──────●  ●──────●
            \    /    \    /
develop      ●──●──●──●──●──●──●──●
              \   /\   /\   /
feature        ●─●  ●─●  ●─●
```

**GitHub Flow (Simpler):**
```
main     ●────●────●────●────●
                \    /
feature          ●──●
```

| Strategy | Branches | Best For |
|----------|----------|----------|
| **Git Flow** | main, develop, feature, release, hotfix | Scheduled releases, complex projects |
| **GitHub Flow** | main, feature | Continuous deployment, simple projects |
| **Trunk-Based** | main only (short-lived branches) | CI/CD, experienced teams |

---

## Merging

### Fast-Forward Merge

```bash
# When feature branch is directly ahead of main
main:     A---B
feature:       C---D

# git merge feature
main:     A---B---C---D
```

```bash
git checkout main
git merge feature
```

### Three-Way Merge

```bash
# When both branches have diverged
main:     A---B---C
               \
feature:        D---E

# git merge feature
main:     A---B---C---F (merge commit)
               \     /
feature:        D---E
```

```bash
git checkout main
git merge feature
# Creates a merge commit
```

### Merge with No Fast-Forward

```bash
# Always create a merge commit
git merge --no-ff feature
```

### Aborting a Merge

```bash
git merge --abort             # Abort merge in progress
git reset --hard HEAD         # Discard all merge changes
```

---

## Rebasing

Rebasing moves a branch to start from a different commit, creating a linear history.

```
Before rebase:
main:     A---B---C
               \
feature:        D---E

After rebase:
main:     A---B---C
                    \
feature:            D'---E'
```

```bash
git checkout feature
git rebase main

# Interactive rebase (powerful!)
git rebase -i main            # Squash, reorder, edit commits
git rebase -i HEAD~3          # Rebase last 3 commits
```

### Interactive Rebase Options

```
pick abc1234 First commit
squash def5678 Second commit
reword ghi9012 Third commit
fixup jkl3456 Fourth commit (merge into previous)
drop mno7890 Remove this commit
```

### Golden Rule of Rebasing

> **Never rebase commits that have been pushed to a shared branch.**

```
❌ On main branch:
   git rebase origin/main
   git push
   # Your teammates' history is now broken!

✅ Only rebase your local feature branches before pushing
```

---

## Stashing

Temporarily save changes without committing.

```bash
# Stash current changes
git stash
git stash push -m "WIP: login form"

# List stashes
git stash list

# Apply stash (keeps it in list)
git stash apply
git stash apply stash@{1}

# Pop stash (applies and removes)
git stash pop

# Drop stash
git stash drop stash@{0}

# Clear all stashes
git stash clear
```

---

## Working with Remotes

### Remote Commands

```bash
# View remotes
git remote -v

# Add remote
git remote add origin https://github.com/user/repo.git

# Rename remote
git remote rename origin upstream

# Remove remote
git remote remove origin

# Change remote URL
git remote set-url origin https://new-url.git
```

### Fetch, Pull, and Push

```bash
# Download changes from remote (doesn't merge)
git fetch origin
git fetch --all

# Download and merge changes
git pull origin main

# Equivalent to:
git fetch origin
git merge origin/main

# Push changes
git push origin main
git push -u origin feature-x  # Set upstream tracking

# Force push (DANGEROUS — overwrites remote history)
git push --force-with-lease   # Safer force push
```

### Tracking Branches

```bash
# Set upstream tracking
git branch --set-upstream-to=origin/main main

# Push and set upstream in one command
git push -u origin feature-x

# After setting upstream, just:
git pull
git push
```

---

## Undoing Changes

### Discarding Local Changes

```bash
# Discard changes in working directory
git restore file.txt
git checkout -- file.txt      # Older syntax

# Discard all changes
git restore .

# Unstage files
git restore --staged file.txt
```

### Amending Commits

```bash
# Add changes to last commit
git add forgotten-file.txt
git commit --amend

# Change last commit message
git commit --amend -m "New message"

# Amend without changing message
git commit --amend --no-edit

# ⚠️ Don't amend pushed commits!
```

### Reverting Commits

```bash
# Create a new commit that undoes a previous commit
git revert abc1234

# Revert a merge commit
git revert -m 1 abc1234       # Specify parent to keep
```

### Resetting (DANGEROUS)

```bash
# Soft reset — moves HEAD, keeps changes staged
git reset --soft HEAD~1

# Mixed reset — moves HEAD, keeps changes unstaged (default)
git reset --mixed HEAD~1
git reset HEAD~1

# Hard reset — DESTROYS changes permanently
git reset --hard HEAD~1
```

| Command | HEAD | Staging | Working Dir |
|---------|------|---------|-------------|
| `reset --soft` | Moves | Keeps | Keeps |
| `reset --mixed` | Moves | Clears | Keeps |
| `reset --hard` | Moves | Clears | Clears |
| `checkout` | Moves | Clears | Keeps |
| `revert` | Creates new commit | — | — |

---

## Resolving Merge Conflicts

When Git can't automatically merge, you'll see:

```
<<<<<<< HEAD
console.log("Hello from main");
=======
console.log("Hello from feature");
>>>>>>> feature
```

### Conflict Resolution Steps

```bash
# 1. Git tells you which files have conflicts
git status

# 2. Open conflicted files and resolve
#    - Choose one side, or combine both
#    - Remove conflict markers

# 3. Stage resolved files
git add resolved-file.txt

# 4. Complete the merge
git commit                    # For merges
git rebase --continue         # For rebases
```

### Using Merge Tools

```bash
# Configure merge tool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd "code --wait $MERGED"

# Launch merge tool
git mergetool
```

---

## .gitignore

Specify files Git should ignore.

```gitignore
# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
*.exe
*.dll

# Environment files
.env
.env.local
.env.*.local

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Test coverage
coverage/
.nyc_output/

# Database
*.sqlite
*.sqlite3

# Temp files
tmp/
temp/
*.tmp
```

### .gitignore Rules

```bash
# Ignore all .txt files
*.txt

# But not important.txt
!important.txt

# Ignore all files in directory
build/

# Ignore files only in root
/build

# Ignore files with specific pattern
log-*.txt
```

---

## Advanced Git

### Cherry-Picking

Apply a specific commit from another branch.

```bash
git cherry-pick abc1234       # Pick one commit
git cherry-pick abc1234 def5678  # Pick multiple
git cherry-pick -n abc1234    # Pick without committing
```

### Bisect (Binary Search for Bugs)

Find which commit introduced a bug.

```bash
git bisect start
git bisect bad                # Current commit has bug
git bisect good v1.0          # v1.0 was fine
# Git checks out middle commit
# Test and mark good or bad
git bisect good
# ...repeat until found
git bisect reset
```

### Reflog (Git's Safety Net)

Git records every change to HEAD.

```bash
git reflog
# Output:
# abc1234 HEAD@{0}: commit: Add feature
# def5678 HEAD@{1}: checkout: moving from main to feature
# ghi9012 HEAD@{2}: commit: Fix bug

# Recover deleted branch
git checkout -b recovered-branch abc1234

# Recover after reset
git reset --hard HEAD@{1}
```

### Submodules

Include one Git repo inside another.

```bash
# Add submodule
git submodule add https://github.com/user/lib.git

# Clone with submodules
git clone --recurse-submodules https://github.com/user/repo.git

# Update submodules
git submodule update --init --recursive
```

---

## GitHub Workflow

### Fork and Pull Request Workflow

```
1. Fork repository on GitHub
2. Clone your fork: git clone https://github.com/your-user/repo.git
3. Add upstream remote: git remote add upstream https://github.com/original/repo.git
4. Create feature branch: git checkout -b feature-x
5. Make changes and commit
6. Push to your fork: git push origin feature-x
7. Open Pull Request on GitHub
8. Address review comments
9. Merge PR
```

### Keeping Your Fork Updated

```bash
# Fetch upstream changes
git fetch upstream

# Merge into your main
git checkout main
git merge upstream/main

# Push to your fork
git push origin main
```

### Pull Request Best Practices

- Keep PRs small and focused (one feature/fix per PR)
- Write clear PR descriptions with "What" and "Why"
- Link to related issues
- Add screenshots for UI changes
- Ensure CI passes before requesting review
- Respond to feedback promptly

---

## Common Mistakes

### Mistake 1: Committing Sensitive Data

```bash
# ❌ Committed .env file!
git add .
git commit -m "Initial commit"
git push

# ✅ Remove from history (before push)
git rm --cached .env
git commit --amend

# ✅ If already pushed, use BFG Repo-Cleaner or filter-branch
# (This rewrites history — coordinate with team!)
```

### Mistake 2: Committing Large Files

```bash
# ❌ Committed node_modules or build artifacts
git add .
git commit -m "Add everything"

# ✅ Use .gitignore
# node_modules/
# dist/

# ✅ If already committed, use Git LFS for large files
git lfs track "*.psd"
git lfs track "*.zip"
```

### Mistake 3: Pushing to Main Directly

```bash
# ❌ Direct push to main
git checkout main
git commit -am "Quick fix"
git push origin main

# ✅ Use feature branches and Pull Requests
git checkout -b fix-login-bug
# ...make changes...
git push origin fix-login-bug
# Open PR, get review, merge
```

### Mistake 4: Rebase on Shared Branches

```bash
# ❌ Rebasing main after others have pulled
git checkout main
git rebase feature-x
git push --force
# Everyone else's main is now out of sync!

# ✅ Only rebase your local feature branches
git checkout my-feature
git rebase main
# Before pushing for the first time
```

### Mistake 5: Not Writing Good Commit Messages

```bash
# ❌ Bad commit messages
git commit -m "fix"
git commit -m "asdf"
git commit -m "Updates"

# ✅ Good commit messages
git commit -m "Fix login timeout issue for OAuth users

- Increase token refresh interval from 5min to 15min
- Add retry logic for failed refresh attempts
- Update tests to cover edge cases

Fixes #234"
```

**Commit Message Format:**
```
<type>(<scope>): <subject>

<body>

<footer>
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

---

## Practice Exercises

### Exercise 1: Git Workflow Simulation

Practice the complete feature branch workflow:

```bash
# 1. Create a repo
git init my-project && cd my-project

# 2. Make initial commit
echo "# My Project" > README.md
git add README.md && git commit -m "Initial commit"

# 3. Create feature branch
git checkout -b add-auth

# 4. Make multiple commits
# ...add auth.js, commit...
# ...update README, commit...

# 5. Meanwhile, "hotfix" on main
git checkout main
# ...make hotfix, commit...

# 6. Merge feature branch
git merge add-auth

# 7. View the graph
git log --graph --oneline --all
```

### Exercise 2: Conflict Resolution

Create and resolve a merge conflict:

```bash
# 1. Create a file on main
echo "line 1" > file.txt
git add file.txt && git commit -m "Add file"

# 2. Create branch, modify line 1
git checkout -b branch-a
# Change line 1 to "branch a change"
git commit -am "Branch A change"

# 3. Back to main, modify same line
git checkout main
# Change line 1 to "main change"
git commit -am "Main change"

# 4. Merge and resolve
git merge branch-a
# Resolve conflict, commit
```

### Exercise 3: Interactive Rebase

Clean up your commit history:

```bash
# Make 5 commits with "WIP" and typo messages
# Use interactive rebase to:
# - Squash WIP commits
# - Reword typo messages
# - Drop an accidental debug commit
git rebase -i HEAD~5
```

### Exercise 4: Recover Lost Work

Simulate and recover from disasters:

```bash
# 1. Make a commit, then reset hard
git commit -am "Important work"
git reset --hard HEAD~1
# Oops! Use reflog to recover

# 2. Delete a branch with unmerged work
git branch -D feature-x
# Recover from reflog

# 3. Amend a pushed commit
git commit --amend
# Fix with force-with-lease
```

### Exercise 5: Team Workflow Setup

Set up a proper team workflow:

```bash
# 1. Create a repo with branch protection rules
# 2. Set up .gitignore for a Node.js project
# 3. Configure pre-commit hooks (husky)
# 4. Practice:
#    - Feature branch workflow
#    - Code review simulation
#    - Handling merge conflicts
#    - Reverting a bad merge
```

---

## Summary

- **Git** is a distributed version control system — every clone is a complete repository
- Git stores **snapshots**, not diffs — each commit is a full filesystem snapshot
- Files exist in three states: **Working Directory** → **Staging Area** → **Repository**
- **Branches** are lightweight pointers to commits — create them freely
- **Merging** combines branches; **rebasing** creates linear history
- **Never rebase commits that have been pushed** to shared branches
- **Stash** temporarily saves work without committing
- **Remotes** connect your local repo to others (origin, upstream)
- `.gitignore` prevents tracking unwanted files
- **Reflog** is your safety net — it records every HEAD change
- Use **feature branches** and **Pull Requests** for team collaboration
- Write **clear commit messages** that explain why, not just what
- Use `--force-with-lease` instead of `--force` when pushing rewritten history

---

## Next Steps

- **Linux Fundamentals** — master the command line where Git was born
- **Backend Development with Node.js** — apply Git workflows to real projects
- **System Design** — learn branching strategies for large teams
- Explore **Git hooks**, **GitHub Actions**, and **Git LFS** for advanced workflows

Happy coding! 🚀
