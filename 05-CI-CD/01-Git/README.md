# Practical Git Command Cheat Sheet

A compact reference for useful Git commands in day-to-day DevOps work.

## Check repository state

### `git status`
Shows the current branch, modified files, staged files, and untracked files.

```bash
git status
```

### `git branch`
Lists local branches.

```bash
git branch
```

The `*` marks the active branch.

Show local and remote-tracking branches:

```bash
git branch -a
```

### `git log`
Compact history:

```bash
git log --oneline
```

Visual branch history:

```bash
git log --oneline --graph --all
```

### `git diff`
Show unstaged changes:

```bash
git diff
```

Show staged changes:

```bash
git diff --staged
```

---

## Create or obtain repositories

### Initialize a repository

```bash
git init
```

### Create a bare repository

```bash
git init --bare /opt/project.git
```

Bare repositories are commonly used as central remotes.

### Clone a repository

```bash
git clone <repository-url>
```

Clone into a specific directory:

```bash
git clone <source> <destination>
```

---

## Stage and commit changes

### Stage one file

```bash
git add file.txt
```

### Stage all changes under the current directory

```bash
git add .
```

### Commit staged changes

```bash
git commit -m "Add configuration file"
```

### Commit modifications to tracked files

```bash
git commit -am "Update configuration"
```

Note: `git commit -am` does not include new untracked files.

---

## Branches

Create a branch:

```bash
git branch feature-login
```

Create and switch:

```bash
git checkout -b feature-login
```

Modern equivalent:

```bash
git switch -c feature-login
```

Switch branches:

```bash
git checkout master
```

or:

```bash
git switch master
```

Delete a merged branch:

```bash
git branch -d feature-login
```

Force delete:

```bash
git branch -D feature-login
```

---

## Merge branches

Important rule:

```text
Switch to the destination branch
        ↓
Merge the source branch
```

Example:

```bash
git checkout master
git merge datacenter
```

This means: merge `datacenter` into `master`.

---

## Remote repositories

Show remotes:

```bash
git remote -v
```

Add a remote:

```bash
git remote add origin <repository-url>
```

Push a branch:

```bash
git push origin master
```

Push and set upstream:

```bash
git push -u origin feature-login
```

Fetch remote changes without integrating them:

```bash
git fetch origin
```

Fetch and integrate remote changes:

```bash
git pull
```

Inspect remote branches:

```bash
git ls-remote --heads origin
```

---

## Inspect commits and branch contents

Show a commit:

```bash
git show <commit-id>
```

Show a file from a specific branch:

```bash
git show master:file.txt
```

Example:

```bash
git show datacenter:index.html
```

This is especially useful because it proves that the file is committed on that branch.

Show current commit hash:

```bash
git rev-parse HEAD
```

Show current branch name:

```bash
git rev-parse --abbrev-ref HEAD
```

---

## Undo mistakes

Discard unstaged changes:

```bash
git restore file.txt
```

Unstage a file:

```bash
git restore --staged file.txt
```

Undo the last commit but keep changes staged:

```bash
git reset --soft HEAD~1
```

Undo the last commit and leave changes unstaged:

```bash
git reset HEAD~1
```

Safely reverse a commit with a new commit:

```bash
git revert <commit-id>
```

---

## Core Git mental model

```text
Working Directory
      |
      | git add
      v
Staging Area
      |
      | git commit
      v
Local Repository
      |
      | git push
      v
Remote Repository
```

Changes coming from the remote:

```text
Remote Repository
      |
      | git fetch / git pull
      v
Local Repository
      |
      v
Working Directory
```

---

## 12 commands to learn first

```bash
git status
git branch
git checkout
git checkout -b
git add
git commit
git log --oneline --graph --all
git merge
git remote -v
git push
git pull
git show
```

These cover a large percentage of practical Git work.

---

## Typical DevOps workflow

```bash
git checkout master
git pull

git checkout -b feature-update

# edit files

git status
git add .
git commit -m "Implement feature update"

git checkout master
git merge feature-update

git push origin feature-update
git push origin master
```

---

## Useful troubleshooting commands

Check repository state:

```bash
git status
```

Visualize history:

```bash
git log --oneline --graph --all
```

Check remotes:

```bash
git remote -v
```

Check remote branches:

```bash
git ls-remote --heads origin
```

Inspect a file on another branch:

```bash
git show branch-name:path/to/file
```

Check whether a repository is bare:

```bash
git rev-parse --is-bare-repository
```

---

## Common errors

### Dubious ownership

```text
fatal: detected dubious ownership in repository
```

If the repository is trusted:

```bash
git config --global --add safe.directory <repository-path>
```

This changes Git's trust decision, but does not change filesystem permissions.

### Unable to create `.git/index.lock`

```text
Unable to create '.git/index.lock': Permission denied
```

Usually means the current user lacks write permission.

Check:

```bash
ls -ld .
ls -ld .git
```

### Push rejected

Useful checks:

```bash
git status
git branch
git remote -v
git log --oneline --graph --all
git ls-remote --heads origin
```

---

## Quick reference

| Command | Purpose |
|---|---|
| `git status` | Show repository state |
| `git branch` | List local branches |
| `git branch -a` | List local and remote-tracking branches |
| `git checkout branch` | Switch branches |
| `git checkout -b branch` | Create and switch branch |
| `git switch branch` | Modern branch switching |
| `git add file` | Stage a file |
| `git add .` | Stage current directory changes |
| `git commit -m "msg"` | Create a commit |
| `git log --oneline` | Compact history |
| `git log --oneline --graph --all` | Visual history |
| `git diff` | Show unstaged changes |
| `git diff --staged` | Show staged changes |
| `git merge branch` | Merge branch into current branch |
| `git remote -v` | Show remotes |
| `git push origin branch` | Push branch |
| `git pull` | Fetch and integrate changes |
| `git fetch` | Download remote changes |
| `git show branch:file` | Inspect committed file |
| `git ls-remote --heads origin` | Inspect remote branches |
| `git restore file` | Discard unstaged changes |
| `git restore --staged file` | Unstage file |
| `git revert <commit>` | Safely reverse a commit |

---

## Final advice

Do not memorize Git as a pile of unrelated commands.

Understand this flow:

```text
Working files
     ↓
Staging
     ↓
Commits
     ↓
Remote repository
```

Then remember that branches are simply pointers into commit history.

Once that mental model becomes natural, most Git commands become much easier to understand and remember.
