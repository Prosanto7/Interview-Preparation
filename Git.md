# 🔀 Git & Version Control

> Master Git from basics to advanced workflows for professional software development.

---

## 📚 Table of Contents

1. [What is Version Control?](#-what-is-version-control)
2. [Git Basics](#-git-basics)
3. [Branching Strategies](#-branching-strategies)
4. [Common Git Workflows](#-common-git-workflows)
5. [Advanced Git Commands](#-advanced-git-commands)
6. [Best Practices](#-best-practices)
7. [Troubleshooting](#-troubleshooting)
8. [Git Hosting Platforms](#-git-hosting-platforms)
9. [Resources](#-resources)

---

## 🎯 What is Version Control?

**Version Control System (VCS)** tracks changes to files over time, allowing you to:
- Recall specific versions
- Compare changes
- Collaborate with others
- Revert to previous states
- Understand who changed what and when

---

### Why Git?

| Feature | Benefit |
|---------|---------|
| **Distributed** | Every developer has full history |
| **Fast** | Operations are local and quick |
| **Branching** | Lightweight and powerful |
| **Popular** | Industry standard, huge community |
| **Open Source** | Free and extensible |

---

## 🚀 Git Basics

### Installation

```bash
# Ubuntu/Debian
sudo apt install git

# macOS (via Homebrew)
brew install git

# Windows
# Download from https://git-scm.com

# Verify installation
git --version
```

---

### Initial Configuration

```bash
# Set your identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default editor
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "vim"          # Vim

# Set default branch name
git config --global init.defaultBranch main

# View configuration
git config --list
git config user.name
```

---

### Basic Commands

#### Creating a Repository

```bash
# Initialize new repository
git init

# Clone existing repository
git clone https://github.com/user/repo.git
git clone https://github.com/user/repo.git my-folder
```

---

#### Staging and Committing

```bash
# Check status
git status

# Add files to staging area
git add file.txt
git add .                    # Add all files
git add *.js                 # Add all JS files
git add -p                   # Interactive staging

# Remove from staging
git reset file.txt
git reset                    # Unstage all

# Commit changes
git commit -m "Add feature X"
git commit -am "Add feature Y"  # Add and commit tracked files

# Amend last commit
git commit --amend
git commit --amend --no-edit
```

---

#### Viewing History

```bash
# View commit history
git log
git log --oneline
git log --graph --oneline --all
git log --author="John"
git log --since="2 weeks ago"
git log --grep="bug fix"

# View specific file history
git log file.txt
git log -p file.txt          # With changes

# Show commit details
git show <commit-hash>
git show HEAD                # Latest commit
```

---

#### Working with Remotes

```bash
# View remotes
git remote -v

# Add remote
git remote add origin https://github.com/user/repo.git

# Fetch changes (don't merge)
git fetch origin

# Pull changes (fetch + merge)
git pull origin main
git pull --rebase origin main

# Push changes
git push origin main
git push -u origin main      # Set upstream
git push --all               # Push all branches
git push --tags              # Push tags
```

---

## 🌿 Branching Strategies

### What is a Branch?

A **branch** is a pointer to a specific commit, allowing parallel development.

```bash
# List branches
git branch
git branch -a                # Include remote branches

# Create branch
git branch feature-x

# Switch branch
git checkout feature-x
git switch feature-x         # Newer syntax

# Create and switch
git checkout -b feature-x
git switch -c feature-x

# Delete branch
git branch -d feature-x      # Safe delete
git branch -D feature-x      # Force delete

# Rename branch
git branch -m old-name new-name
```

---

### Merging

```bash
# Merge branch into current branch
git merge feature-x

# Merge with commit message
git merge feature-x -m "Merge feature X"

# Abort merge
git merge --abort

# Merge strategies
git merge --no-ff feature-x  # Always create merge commit
git merge --squash feature-x # Squash all commits
```

---

### Rebasing

**Rebase** replays commits on top of another branch.

```bash
# Rebase current branch onto main
git rebase main

# Interactive rebase (edit history)
git rebase -i HEAD~3

# Continue after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort
```

#### Merge vs Rebase

| Aspect | Merge | Rebase |
|--------|-------|--------|
| **History** | Preserves branch history | Linear history |
| **Commits** | Creates merge commit | Replays commits |
| **Use Case** | Feature branches | Clean up before merge |
| **Safety** | Non-destructive | Rewrites history |

**Golden Rule**: Never rebase public branches!

---

### Popular Branching Models

#### 1. **Git Flow**

```
main (production-ready)
  ↓
develop (integration)
  ↓
feature/* (new features)
release/* (release preparation)
hotfix/* (production fixes)
```

**Commands:**
```bash
# Start feature
git checkout -b feature/login develop

# Finish feature
git checkout develop
git merge --no-ff feature/login
git branch -d feature/login

# Hotfix
git checkout -b hotfix/security-patch main
# Fix and commit
git checkout main
git merge --no-ff hotfix/security-patch
git checkout develop
git merge --no-ff hotfix/security-patch
```

---

#### 2. **GitHub Flow** (Simpler)

```
main (always deployable)
  ↓
feature/* (all changes)
```

**Workflow:**
1. Create branch from main
2. Make changes and commit
3. Open Pull Request
4. Review and discuss
5. Merge to main
6. Deploy

---

#### 3. **GitLab Flow**

Combines feature branches with environment branches.

```
main
  ↓
production
  ↓
feature/*
```

---

#### 4. **Trunk-Based Development**

Everyone commits to main (trunk) frequently.

```
main
  ↓
short-lived feature branches (< 1 day)
```

---

## 🔄 Common Git Workflows

### Feature Branch Workflow

```bash
# 1. Update main
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/user-auth

# 3. Work and commit
git add .
git commit -m "Add login functionality"

# 4. Push to remote
git push -u origin feature/user-auth

# 5. Create Pull Request on GitHub/GitLab

# 6. After review, merge and delete branch
git checkout main
git pull origin main
git branch -d feature/user-auth
```

---

### Forking Workflow (Open Source)

```bash
# 1. Fork repository on GitHub

# 2. Clone your fork
git clone https://github.com/YOUR-USERNAME/repo.git

# 3. Add upstream remote
git remote add upstream https://github.com/ORIGINAL-OWNER/repo.git

# 4. Create feature branch
git checkout -b fix-typo

# 5. Make changes and commit
git commit -am "Fix typo in README"

# 6. Push to your fork
git push origin fix-typo

# 7. Create Pull Request to upstream

# 8. Keep fork synced
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

---

## ⚡ Advanced Git Commands

### Stashing

Temporarily save changes without committing.

```bash
# Stash changes
git stash
git stash save "Work in progress on feature X"

# List stashes
git stash list

# Apply latest stash
git stash apply
git stash pop               # Apply and remove

# Apply specific stash
git stash apply stash@{2}

# Delete stash
git stash drop stash@{0}
git stash clear             # Delete all
```

---

### Cherry-Picking

Apply specific commits to current branch.

```bash
# Cherry-pick single commit
git cherry-pick <commit-hash>

# Cherry-pick range
git cherry-pick A^..B

# Cherry-pick without committing
git cherry-pick -n <commit-hash>
```

---

### Reset

Move branch pointer to different commit.

```bash
# Soft reset (keep changes staged)
git reset --soft HEAD~1

# Mixed reset (keep changes unstaged) - DEFAULT
git reset HEAD~1
git reset --mixed HEAD~1

# Hard reset (discard changes)
git reset --hard HEAD~1
git reset --hard origin/main

# Reset specific file
git checkout HEAD -- file.txt
```

---

### Revert

Create new commit that undoes changes.

```bash
# Revert specific commit
git revert <commit-hash>

# Revert without committing
git revert -n <commit-hash>

# Revert merge commit
git revert -m 1 <merge-commit-hash>
```

---

### Reflog

View history of HEAD movements (safety net).

```bash
# View reflog
git reflog

# Recover lost commit
git reflog
git checkout <commit-hash>
git cherry-pick <commit-hash>
```

---

### Bisect

Binary search to find bug-introducing commit.

```bash
# Start bisect
git bisect start

# Mark current as bad
git bisect bad

# Mark old commit as good
git bisect good <commit-hash>

# Git will checkout commits to test
# After testing each:
git bisect good  # or
git bisect bad

# When found, reset
git bisect reset
```

---

### Tags

Mark specific points in history (releases).

```bash
# List tags
git tag

# Create lightweight tag
git tag v1.0.0

# Create annotated tag
git tag -a v1.0.0 -m "Release version 1.0.0"

# Tag specific commit
git tag -a v0.9.0 <commit-hash> -m "Beta release"

# Push tags
git push origin v1.0.0
git push --tags

# Delete tag
git tag -d v1.0.0
git push origin --delete v1.0.0
```

---

### Submodules

Include other Git repositories.

```bash
# Add submodule
git submodule add https://github.com/user/lib.git lib

# Clone repository with submodules
git clone --recurse-submodules https://github.com/user/repo.git

# Update submodules
git submodule update --init --recursive
git submodule update --remote

# Remove submodule
git submodule deinit lib
git rm lib
```

---

## ✅ Best Practices

### Commit Messages

#### Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

#### Types
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Formatting
- `refactor`: Code restructuring
- `test`: Adding tests
- `chore`: Maintenance

#### Examples

```bash
git commit -m "feat(auth): add login functionality"

git commit -m "fix(api): handle null response from user endpoint

Previously, a null response would crash the app.
Now we return a default user object.

Closes #123"
```

---

### Conventional Commits

Follow the [Conventional Commits](https://www.conventionalcommits.org/) specification.

```
feat: allow provided config object to extend other configs
fix: prevent racing of requests
docs: correct spelling of CHANGELOG
style: format code with prettier
refactor: drop support for Node 6
perf: improve performance with lazy load implementation for images
test: add missing tests for auth
build: update dependencies
ci: update GitHub Actions workflow
chore: update .gitignore
```

---

### .gitignore

Ignore files that shouldn't be tracked.

```bash
# .gitignore

# Dependencies
node_modules/
vendor/

# Environment files
.env
.env.local

# Build output
dist/
build/
*.log

# IDE files
.vscode/
.idea/
*.swp

# OS files
.DS_Store
Thumbs.db

# Temporary files
*.tmp
*.bak
```

**Templates:** [github.com/github/gitignore](https://github.com/github/gitignore)

---

### Security

```bash
# Never commit secrets
# Use .gitignore and environment variables

# If you accidentally commit a secret:
# 1. Rotate the secret immediately
# 2. Remove from history:

# Using git filter-branch
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch .env" \
  --prune-empty --tag-name-filter cat -- --all

# Using BFG Repo-Cleaner (easier)
bfg --delete-files .env
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Force push
git push --force --all
```

---

### Keep Commits Atomic

- One logical change per commit
- Commits should be independently reversible
- Makes bisecting and reverting easier

❌ **Bad:**
```bash
git commit -m "Add login, fix bug, update docs"
```

✅ **Good:**
```bash
git commit -m "feat: add login functionality"
git commit -m "fix: resolve null pointer in auth"
git commit -m "docs: update API documentation"
```

---

## 🔧 Troubleshooting

### Undo Last Commit (Keep Changes)

```bash
git reset --soft HEAD~1
```

---

### Undo Last Commit (Discard Changes)

```bash
git reset --hard HEAD~1
```

---

### Fix Commit Message

```bash
# Last commit
git commit --amend -m "New message"

# Older commit (interactive rebase)
git rebase -i HEAD~3
# Change 'pick' to 'reword' for commits to edit
```

---

### Resolve Merge Conflicts

```bash
# 1. Identify conflicts
git status

# 2. Edit conflicted files
# Look for conflict markers:
<<<<<<< HEAD
Your changes
=======
Their changes
>>>>>>> branch-name

# 3. After resolving
git add resolved-file.txt

# 4. Complete merge
git commit
```

---

### Recover Deleted Branch

```bash
# Find commit hash
git reflog

# Recreate branch
git checkout -b recovered-branch <commit-hash>
```

---

### Clean Working Directory

```bash
# Preview what will be removed
git clean -n

# Remove untracked files
git clean -f

# Remove untracked files and directories
git clean -fd

# Remove ignored files too
git clean -fdx
```

---

### Ignore Already Tracked File

```bash
# Stop tracking but keep file
git rm --cached file.txt

# Add to .gitignore
echo "file.txt" >> .gitignore

git commit -m "Stop tracking file.txt"
```

---

### Change Remote URL

```bash
# View current URL
git remote -v

# Change URL
git remote set-url origin https://github.com/user/new-repo.git

# Verify
git remote -v
```

---

## 🌐 Git Hosting Platforms

### GitHub

**Features:**
- Pull Requests
- GitHub Actions (CI/CD)
- GitHub Pages (hosting)
- Projects and Issues
- Code review tools

**CLI:**
```bash
# Install GitHub CLI
brew install gh

# Authenticate
gh auth login

# Create repository
gh repo create my-project --public

# Create Pull Request
gh pr create

# View PRs
gh pr list
```

---

### GitLab

**Features:**
- Merge Requests
- GitLab CI/CD (built-in)
- Container Registry
- Wiki
- Issue tracking

---

### Bitbucket

**Features:**
- Jira integration
- Pipelines (CI/CD)
- Code review
- Branch permissions

---

## 🎓 Advanced Topics

### Git Hooks

Automate actions on Git events.

```bash
# .git/hooks/pre-commit
#!/bin/bash
npm test
if [ $? -ne 0 ]; then
  echo "Tests failed! Commit aborted."
  exit 1
fi
```

**Common Hooks:**
- `pre-commit`: Before commit
- `pre-push`: Before push
- `post-commit`: After commit
- `post-merge`: After merge

**Tools:**
- [Husky](https://typicode.github.io/husky/) (JavaScript)
- [pre-commit](https://pre-commit.com/) (Python)

---

### Git LFS (Large File Storage)

For large binary files.

```bash
# Install
git lfs install

# Track large files
git lfs track "*.psd"
git lfs track "*.mp4"

# Add .gitattributes
git add .gitattributes

# Commit as normal
git add large-file.psd
git commit -m "Add design file"
```

---

### Signed Commits

Verify commit authenticity with GPG.

```bash
# Generate GPG key
gpg --gen-key

# List keys
gpg --list-secret-keys --keyid-format LONG

# Configure Git
git config --global user.signingkey <key-id>
git config --global commit.gpgsign true

# Sign commit
git commit -S -m "Signed commit"
```

---

## 📚 Resources

### Official Documentation
- [Git Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/en/v2) (Free)
- [Git Reference](https://git-scm.com/docs)

### Interactive Learning
- [Learn Git Branching](https://learngitbranching.js.org/)
- [Git-it](https://github.com/jlord/git-it-electron)
- [Oh My Git!](https://ohmygit.org/)

### Cheat Sheets
- [GitHub Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Atlassian Git Cheat Sheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)

### Videos
- [Git & GitHub Crash Course (Traversy Media)](https://www.youtube.com/watch?v=SWYqp7iY_Tc)
- [Advanced Git Tutorial (The Net Ninja)](https://www.youtube.com/playlist?list=PL4cUxeGkcC9goXbgTDQ0n_4TBzOO0ocPR)

### Books
- **"Pro Git"** by Scott Chacon (Free online)
- **"Git Pocket Guide"** by Richard E. Silverman

---

## ✅ Git Command Cheat Sheet

### Setup
```bash
git config --global user.name "Name"
git config --global user.email "email@example.com"
```

### Create
```bash
git init
git clone <url>
```

### Local Changes
```bash
git status
git add <file>
git commit -m "message"
```

### Branching
```bash
git branch
git checkout -b <branch>
git merge <branch>
```

### Update & Publish
```bash
git pull
git push origin <branch>
```

### Undo
```bash
git reset --hard HEAD
git revert <commit>
```

### History
```bash
git log
git diff
```

---

**Remember:** Git is powerful but can be complex. Don't be afraid to ask for help, and always commit often!
