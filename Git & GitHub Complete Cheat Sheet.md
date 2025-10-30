# Git & GitHub Complete Cheat Sheet

## Table of Contents
- [Basic Concepts](#basic-concepts)
- [Installation & Setup](#installation--setup)
- [Repository Operations](#repository-operations)
- [Basic Workflow](#basic-workflow)
- [Branching & Merging](#branching--merging)
- [Undoing Changes](#undoing-changes)
- [Remote Repositories](#remote-repositories)
- [Collaboration](#collaboration)
- [Advanced Techniques](#advanced-techniques)
- [GitHub Specific](#github-specific)
- [Troubleshooting](#troubleshooting)

## Basic Concepts

### What is Git?
- Distributed Version Control System
- Tracks changes in source code
- Enables collaboration

### Key Terminology
- **Repository**: Project folder tracked by Git
- **Commit**: Snapshot of changes
- **Branch**: Independent line of development
- **Clone**: Copy of remote repository
- **Fork**: Copy of someone else's repository
- **Pull Request**: Proposal to merge changes
- **HEAD**: Current branch reference

## Installation & Setup

### Install Git
```bash
# Ubuntu/Debian
sudo apt update && sudo apt install git

# macOS
brew install git

# Windows
# Download from git-scm.com
```

### Configure Git
```bash
# Set user identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Set default editor
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "vim"          # Vim

# Color output
git config --global color.ui auto

# Check configuration
git config --list
```

## Repository Operations

### Creating Repositories
```bash
# Initialize new repository
git init

# Clone existing repository
git clone https://github.com/username/repo.git
git clone https://github.com/username/repo.git my-folder-name
git clone git@github.com:username/repo.git  # SSH

# Clone specific branch
git clone -b branch-name https://github.com/username/repo.git
```

### Repository Status
```bash
# Check status
git status
git status -s  # Short format

# Show remote URLs
git remote -v

# Show repository information
git log --oneline -5  # Last 5 commits
git show              # Show latest commit
```

## Basic Workflow

### The Basic Cycle
```bash
# 1. Check status
git status

# 2. Add changes to staging
git add filename.txt          # Specific file
git add folder/               # Specific folder
git add .                     # All changes
git add -A                    # All changes (including deletions)
git add -p                    # Interactive add

# 3. Commit changes
git commit -m "Descriptive commit message"
git commit -am "Add and commit in one step"  # Only for modified files

# 4. Push to remote
git push origin main
```

### Staging Files
```bash
# Stage specific files
git add file1.js file2.css

# Stage all JavaScript files
git add *.js

# Stage all changes in directory
git add src/

# Interactive staging (choose hunks)
git add -p

# Remove file from staging (keep changes)
git reset HEAD filename

# Stage deleted files
git add -u
```

### Committing Changes
```bash
# Basic commit
git commit -m "Fix login bug"

# Commit with description
git commit -m "Title" -m "Description"

# Amend last commit
git commit --amend -m "New message"

# Add to last commit (without changing message)
git add forgotten-file.js
git commit --amend --no-edit
```

### Good Commit Messages
```
feat: add user authentication
^    ^
|    |__ Subject in imperative mood
|
|__ Type: feat, fix, docs, style, refactor, test, chore

Common types:
- feat: New feature
- fix: Bug fix
- docs: Documentation
- style: Formatting, missing semi-colons
- refactor: Code restructuring
- test: Adding tests
- chore: Maintenance tasks
```

## Branching & Merging

### Branch Operations
```bash
# List branches
git branch          # Local branches
git branch -r       # Remote branches
git branch -a       # All branches

# Create branch
git branch new-feature
git checkout -b new-feature  # Create and switch

# Switch branch
git checkout main
git switch main              # Newer command
git checkout -               # Previous branch

# Delete branch
git branch -d branch-name    # Safe delete
git branch -D branch-name    # Force delete

# Rename branch
git branch -m new-name
git branch -m old-name new-name

# Track remote branch
git branch -u origin/branch-name
```

### Merging
```bash
# Merge branch into current branch
git checkout main
git merge feature-branch

# Merge with no-fast-forward (always create merge commit)
git merge --no-ff feature-branch

# Abort merge in progress
git merge --abort

# Check merge conflicts
git status
```

### Rebasing
```bash
# Rebase current branch onto main
git checkout feature
git rebase main

# Interactive rebase (last 3 commits)
git rebase -i HEAD~3

# Continue after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort

# Skip current patch
git rebase --skip
```

### Interactive Rebase Commands
```
pick - use commit
reword - use commit, but edit commit message
edit - use commit, but stop for amending
squash - use commit, but meld into previous commit
fixup - like squash, but discard commit message
drop - remove commit
```

## Undoing Changes

### Undoing Working Directory Changes
```bash
# Discard changes in working directory
git checkout -- filename
git restore filename         # Newer command

# Discard all changes in working directory
git checkout -- .
git restore .               # Newer command

# Restore file from specific branch
git checkout branch-name -- filename
```

### Undoing Staged Changes
```bash
# Unstage file (keep changes)
git reset HEAD filename
git restore --staged filename  # Newer command

# Unstage all files
git reset HEAD
```

### Undoing Commits
```bash
# Soft reset (keep changes staged)
git reset --soft HEAD~1

# Mixed reset (keep changes unstaged) - DEFAULT
git reset --mixed HEAD~1

# Hard reset (discard all changes)
git reset --hard HEAD~1

# Reset to specific commit
git reset --hard abc123

# Revert commit (create new commit that undoes changes)
git revert commit-hash
```

### Recovery Operations
```bash
# View lost commits
git reflog

# Recover from reflog
git reset --hard HEAD@{1}

# Clean untracked files
git clean -n      # Dry run
git clean -f      # Force remove
git clean -fd     # Remove directories too
```

## Remote Repositories

### Working with Remotes
```bash
# Add remote
git remote add origin https://github.com/user/repo.git

# Change remote URL
git remote set-url origin new-url

# Remove remote
git remote remove origin

# Show remote details
git remote show origin
```

### Fetching & Pulling
```bash
# Fetch changes (don't merge)
git fetch origin

# Fetch specific branch
git fetch origin branch-name

# Pull changes (fetch + merge)
git pull origin main

# Pull with rebase
git pull --rebase origin main

# Pull specific branch
git pull origin feature-branch
```

### Pushing
```bash
# Push to remote
git push origin main

# Push new branch (set upstream)
git push -u origin new-branch

# Force push (use with caution!)
git push --force origin main
git push --force-with-lease origin main  # Safer

# Delete remote branch
git push origin --delete branch-name

# Push all tags
git push --tags
```

## Collaboration

### Forking Workflow
```bash
# 1. Fork on GitHub
# 2. Clone your fork
git clone https://github.com/yourname/repo.git

# 3. Add upstream remote
git remote add upstream https://github.com/original/repo.git

# 4. Sync with upstream
git fetch upstream
git merge upstream/main

# 5. Push to your fork
git push origin main
```

### Pull Requests
```bash
# Create feature branch
git checkout -b my-feature

# Make changes and commit
git add .
git commit -m "Add new feature"

# Push to your fork
git push -u origin my-feature

# Then create PR on GitHub

# Update PR with new commits
git add .
git commit -m "Fix issue"
git push origin my-feature
```

### Stashing
```bash
# Stash changes
git stash
git stash push -m "Work in progress"

# List stashes
git stash list

# Apply stash
git stash apply          # Latest stash
git stash apply stash@{1} # Specific stash

# Apply and remove
git stash pop

# Create branch from stash
git stash branch new-branch

# Clear stashes
git stash clear
```

## Advanced Techniques

### Tagging
```bash
# Create lightweight tag
git tag v1.0.0

# Create annotated tag
git tag -a v1.0.0 -m "Release version 1.0.0"

# Show tags
git tag
git show v1.0.0

# Push tags
git push origin v1.0.0
git push origin --tags

# Delete tag
git tag -d v1.0.0
git push origin --delete v1.0.0
```

### Submodules
```bash
# Add submodule
git submodule add https://github.com/user/repo.git path

# Initialize submodules
git submodule init
git submodule update

# Clone with submodules
git clone --recurse-submodules https://github.com/user/repo.git

# Update submodules
git submodule update --remote
```

### Bisect
```bash
# Start bisect session
git bisect start
git bisect bad          # Current commit is bad
git bisect good abc123  # This commit was good

# Mark current commit as good/bad
git bisect good
git bisect bad

# Reset bisect
git bisect reset
```

### Hooks
```bash
# Location
.git/hooks/

# Make hook executable
chmod +x .git/hooks/pre-commit

# Sample pre-commit hook
#!/bin/sh
echo "Running tests before commit..."
npm test
```

## GitHub Specific

### GitHub CLI
```bash
# Install GitHub CLI
# brew install gh (macOS)

# Authenticate
gh auth login

# Create repository
gh repo create my-project --public --clone

# Create PR
gh pr create --title "My feature" --body "Description"

# List PRs
gh pr list

# View PR
gh pr view 123

# Merge PR
gh pr merge 123
```

### GitHub Actions
```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
      - run: npm install
      - run: npm test
```

### GitHub Pages
```bash
# Enable in repository settings
# Settings → Pages → Source: gh-pages branch

# Create gh-pages branch
git checkout --orphan gh-pages
git rm -rf .
echo "Hello World" > index.html
git add index.html
git commit -m "Initial pages"
git push origin gh-pages
```

## Troubleshooting

### Common Issues & Solutions

```bash
# Merge conflicts
git status                    # See conflicts
# Edit files to resolve conflicts
git add resolved-file.js
git commit

# Wrong branch committed
git reset --soft HEAD~1
git checkout correct-branch
git commit -m "Message"

# Accidentally committed to main
git reset --soft HEAD~1
git checkout -b feature-branch
git commit -m "Message"
git checkout main

# Lost commits
git reflog
git reset --hard HEAD@{2}

# Large file committed
git filter-branch --tree-filter 'rm -f large-file.zip' HEAD
git push --force

# Clean up repository
git gc --aggressive
git prune
```

### Debugging
```bash
# Show file history
git log --oneline -- filename

# Show who changed what
git blame filename

# Search commits
git log --grep="search term"
git log -S"functionName"   # Search code changes

# Visual history
git log --oneline --graph --all
```

### Performance
```bash
# Repository size
git count-objects -vH

# Clean up
git gc --aggressive
git prune

# Partial clone (large repos)
git clone --filter=blob:none https://github.com/user/repo.git
```

## Best Practices

### General
- Commit often, perfect later, publish once
- Write descriptive commit messages
- Keep commits focused and atomic
- Use branches for features and fixes
- Review changes before committing

### Branch Strategy
```
main/master   → Production-ready code
develop       → Integration branch
feature/*     → New features
hotfix/*      → Critical production fixes
release/*     → Release preparation
```

### .gitignore Examples
```
# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
*.exe

# Environment files
.env
.env.local

# IDE files
.vscode/
.idea/
*.swp

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
```

### Security
```bash
# Remove sensitive data from history
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch secrets.txt' \
  --prune-empty --tag-name-filter cat -- --all

# Scan for secrets
# Use tools like git-secrets, truffleHog
```

---

## Quick Reference Card

### Daily Commands
```bash
git status
git add .
git commit -m "message"
git push
git pull
```

### Branch Management
```bash
git branch
git checkout -b feature
git merge feature
git branch -d feature
```

### Undo Operations
```bash
git reset --soft HEAD~1    # Undo commit, keep changes staged
git reset --mixed HEAD~1   # Undo commit, keep changes unstaged
git reset --hard HEAD~1    # Undo commit, discard changes
git checkout -- file       # Discard file changes
```

### Remote Operations
```bash
git clone url
git fetch
git pull
git push
git remote -v
```

---

*Remember: Git is a powerful tool - always understand what a command does before running it, especially destructive commands like `reset --hard` and `push --force`!*
