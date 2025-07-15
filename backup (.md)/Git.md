## GIT CONFIGURATION

### Global Configuration
```bash
git config --global user.name "UserNAME"
git config --global user.email "...@gmail.com"
````

### Local Configuration
```bash
git config --local user.name "UserNAME"
git config --local user.email "...@gmail.com"
```

### View Configuration
```bash
git config --global --list
git config --local --list
```

### Create Personal Token on GITLAB
```bash
Command Plate > GitLab: Add Account to VS Code
```

---

## SSH KEY MANAGEMENT

### Creating an RSA Key for GitHub
```bash
ssh-keygen -t rsa -b 4096 -C "...@gmail.com"
cd ~/.ssh/
cat id_rsa.pub
```

### Creating an ED25519 Key for GitLab
```bash
ssh-keygen -t ed25519 -C "...@gmail.com"
cd ~/.ssh/
cat id_ed25519.pub
```

### Activate the SSH Key for GitLab
```bash
ssh -T git@gitlab.com
```

---

## REPOSITORY OPERATIONS
### Initialize a new Git repository
```bash
git init test_project
```

### Staging changes
```bash
git add .  |  git add -A
```

### Commit the staged changes
```bash
git commit -m "adding hello.txt"  
git commit --allow-empty-message -m $''  
git commit --amend --no-edit
```

### Adding a Remote
```bash
git remote add origin git@git...
```

### Pushing Changes
```bash
git push origin branch_name  
git push --force origin branch_name
```

### Pulling Changes
```bash
git pull  
git pull origin main
```

### Git Ignore
```bash
git add .gitignore
git commit -m "Update .gitignore to ignore helpers/"
git rm -r --cached .
git add .
git commit -m "Apply .gitignore rules: untrack ignored files"
```

---

## BRANCH OPERATIONS
### Set Main Branch
```bash
git config --global init.defaultBranch main
cd .../test_project
git branch -m main
```

### Create a New Branch
```bash
git branch v1_branch
```

### Create and Switch to a New Branch
```bash
git checkout -b v2_branch
```

### Switch Branch
```bash
git checkout v1_branch
```

### Merge Branch
```bash
git checkout main
git pull origin main
git merge v1_branch
git add .
git commit -m "Merge: v1_branch into main"
git push origin main
```

### Delete Branch
#### Delete Locally
```bash
git branch -d v1_branch
```

#### Delete Remotely
```bash
git push origin --delete v1_branch
git pull --prune
```

### Checking Branches
```bash
git status
git branch --all
git log --all --oneline
```

---

## RESTORING CHANGES
### See a Reference of All Actions (for recovery)
```bash
git reflog
```

### Restore to Commit
```bash
git checkout commit_hash
```

### Restore All Files Before Commit
```bash
git restore .
```

### Revert all changes in the working directory
```bash
git checkout .
```

### Reset to a Previous Commit (merge state)
```bash
git reset --merge commit_hash
```

### Forcing a Hard Reset
```bash
git checkout main
git reset --hard commit_hash
git push --force origin main
```

### More Restoration Commands
```bash
git stash           # stash changes
git stash push      # hide current changes
git stash apply     # reapply the most recent stash 
git stash list      # view stashed entries
git stash pop       # reapply and remove the stash
git stash clear     # clear all stashed entries
```

---

## GITIGNORE
```bash
# Generic
*.env
*.csv
*.txt

# Ignore Virtual Environment
myEnv/

# Ignore Python bytecode and cache
.py_checkpoints/
.py_cache/
__pycache__/
```