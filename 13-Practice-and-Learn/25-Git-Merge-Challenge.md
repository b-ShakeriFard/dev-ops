# Git Merge Challenge

## Challenge

The Nautilus development team was working with the remote repository:

```text
/opt/games.git
```

A clone already existed on the Storage Server at:

```text
/usr/src/kodekloudrepos/games
```

The requirements were to:

- create a new branch named `datacenter` from `master`;
- copy `/tmp/index.html` into the repository;
- add and commit the file on `datacenter`;
- merge `datacenter` back into `master`;
- push both `datacenter` and `master` to `origin`.

---

# Step-by-Step Guide

## 1. Connect to the Storage Server

```bash
ssh natasha@ststor01
```

## 2. Enter the Repository

```bash
cd /usr/src/kodekloudrepos/games
```

Optional checks:

```bash
pwd
git remote -v
git branch
```

## 3. Start From `master`

```bash
sudo git checkout master
```

Verify that `master` is current:

```bash
sudo git status
```

## 4. Create the Required Branch

```bash
sudo git checkout -b datacenter
```

Verify:

```bash
sudo git branch
```

Expected:

```text
* datacenter
  master
```

## 5. Copy the Required File

```bash
sudo cp /tmp/index.html .
```

Verify:

```bash
cat index.html
```

In this challenge the file contained:

```text
Welcome to Nautilus Group
```

## 6. Stage the File

```bash
sudo git add index.html
```

Check:

```bash
sudo git status
```

## 7. Commit on `datacenter`

```bash
sudo git commit -m "added index.html"
```

Verify that the committed file exists specifically on the new branch:

```bash
sudo git show datacenter:index.html
```

Expected:

```text
Welcome to Nautilus Group
```

## 8. Switch Back to `master`

```bash
sudo git checkout master
```

## 9. Merge the Branch

```bash
sudo git merge datacenter
```

In this challenge Git performed a fast-forward merge:

```text
Fast-forward
 index.html | 1 +
```

## 10. Verify Both Branches

```bash
sudo git show datacenter:index.html
sudo git show master:index.html
```

Both commands should display the file contents.

## 11. Push the New Branch

```bash
sudo git push origin datacenter
```

Expected:

```text
[new branch] datacenter -> datacenter
```

## 12. Push `master`

```bash
sudo git push origin master
```

## 13. Verify the Remote

```bash
sudo git ls-remote --heads origin
```

Expected to include:

```text
<commit-hash> refs/heads/datacenter
<commit-hash> refs/heads/master
```

After the fast-forward merge, both branches may point to the same commit.

---

# Complete Command Sequence

```bash
ssh natasha@ststor01

cd /usr/src/kodekloudrepos/games

sudo git checkout master
sudo git checkout -b datacenter

sudo cp /tmp/index.html .

sudo git add index.html
sudo git commit -m "added index.html"

sudo git show datacenter:index.html

sudo git checkout master
sudo git merge datacenter

sudo git show datacenter:index.html
sudo git show master:index.html

sudo git push origin datacenter
sudo git push origin master

sudo git ls-remote --heads origin
```

---

# Lessons Learned

## 1. Exact Names Matter

Automated lab graders often check exact repository and branch names.

A technically correct workflow can still fail if the required branch is named `datacenter` but another name is used.

Always copy names directly from the task.

## 2. Verify the Branch Before Merging

This command was extremely useful:

```bash
git show datacenter:index.html
```

It proves that `index.html` is actually committed on the `datacenter` branch.

This is stronger than:

```bash
ls
```

because `ls` only shows the current working tree.

## 3. Local and Remote Branches Are Different

Creating:

```bash
git checkout -b datacenter
```

creates a local branch only.

To create it on the remote:

```bash
git push origin datacenter
```

## 4. Understand Fast-Forward Merges

Before the merge:

```text
A---B  master
     \
      C  datacenter
```

If `master` has no additional commits, merging can simply move the `master` pointer:

```text
A---B---C  master, datacenter
```

No extra merge commit is needed.

## 5. Verify the Remote State

This is an excellent final check:

```bash
git ls-remote --heads origin
```

It confirms what branches actually exist on the remote server.

## 6. Permissions and Git Trust Are Separate

A repository can produce:

```text
fatal: detected dubious ownership
```

This can be handled with:

```bash
git config --global --add safe.directory <repo-path>
```

But that does not grant write permissions.

Errors such as:

```text
Unable to create .git/index.lock
```

are filesystem permission problems.

## 7. `sudo` Worked in the Lab, but Is Not Usually Ideal

The lab repository required privileged operations, so commands such as:

```bash
sudo git add ...
sudo git commit ...
```

worked.

In a production environment, the preferred solution is normally correct user/group ownership and permissions rather than routine Git use as root.

---

# Useful Commands

| Command | Purpose |
|---|---|
| `git branch` | List local branches |
| `git checkout master` | Switch to master |
| `git checkout -b datacenter` | Create and switch to a branch |
| `git status` | Inspect working tree and staging area |
| `git add index.html` | Stage a file |
| `git commit -m "message"` | Commit staged changes |
| `git merge datacenter` | Merge branch into current branch |
| `git show branch:file` | Show a committed file from a branch |
| `git push origin branch` | Push a branch |
| `git remote -v` | Show remote repositories |
| `git ls-remote --heads origin` | Show remote branch refs |
| `git log --oneline --graph --all` | Visualize history |

---

# Interview Questions

## 1. What is a Git branch?

A branch is a movable pointer to a commit. It allows independent development without immediately changing another branch such as `master`.

## 2. What does this command do?

```bash
git checkout -b datacenter
```

It creates the `datacenter` branch and switches to it.

Equivalent older-style sequence:

```bash
git branch datacenter
git checkout datacenter
```

## 3. What is the difference between `git add` and `git commit`?

`git add` puts changes into the staging area.

`git commit` records the staged snapshot in repository history.

## 4. What is a fast-forward merge?

It occurs when the target branch has not diverged. Git simply advances the target branch pointer to the newer commit.

## 5. How can you inspect a file from another branch without switching branches?

```bash
git show branch-name:path/to/file
```

Example:

```bash
git show datacenter:index.html
```

## 6. Does creating a local branch create a remote branch?

No.

You must push it:

```bash
git push origin datacenter
```

## 7. What is `origin`?

`origin` is the conventional default name for the remote repository from which a repository was cloned.

Check it with:

```bash
git remote -v
```

## 8. What does `git ls-remote --heads origin` do?

It queries the remote and displays branch references and their commit hashes.

## 9. Why might two branches have the same commit hash?

After a fast-forward merge, both branches can point to exactly the same commit.

## 10. What is the difference between merge and rebase?

`git merge` combines histories and preserves branch structure.

`git rebase` replays commits onto another base and rewrites commit history to create a more linear sequence.

## 11. What causes a merge conflict?

A conflict occurs when Git cannot automatically reconcile competing changes, commonly when both branches modify overlapping lines of the same file.

## 12. What should you check before pushing?

Useful checks include:

```bash
git status
git branch
git log --oneline --graph --all
git show datacenter:index.html
```

These help verify that the correct commit exists on the correct branch before anything is sent to the remote.

---

# Final Result

The successful workflow was:

```text
master
  |
  +-- create datacenter
        |
        +-- copy index.html
        +-- stage
        +-- commit
        |
        v
    datacenter
        |
        +-- merge into master
        |
        +-- push datacenter
        +-- push master
```

Final remote state:

```text
origin/datacenter
origin/master
```

Both branches contained the required `index.html` commit, and the challenge passed successfully.
