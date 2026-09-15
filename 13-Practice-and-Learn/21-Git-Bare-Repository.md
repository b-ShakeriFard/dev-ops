# Creating a Bare Git Repository

## Scenario

The task was to install Git on the storage server and create a **bare Git repository** at:

```text
/opt/beta.git
```

A bare repository is commonly used as a **central/shared remote repository** because it does not contain a working tree.

---

## 1. Install Git

```bash
sudo dnf install -y git
```

Verify:

```bash
git --version
```

---

## 2. Create the Bare Repository

```bash
sudo git init --bare /opt/beta.git
```

Expected output:

```text
Initialized empty Git repository in /opt/beta.git/
```

The `--bare` option creates only Git repository metadata, without checking out project files.

---

## 3. Verify the Repository

Check the directory:

```bash
ls -ld /opt/beta.git
```

Inspect its contents:

```bash
ls /opt/beta.git
```

A bare repository normally contains:

```text
HEAD
config
objects
refs
hooks
info
```

There is no separate `.git` directory because `/opt/beta.git` itself is the repository database.

---

## 4. Confirm That It Is Bare

Use:

```bash
git --git-dir=/opt/beta.git rev-parse --is-bare-repository
```

Expected:

```text
true
```

---

## 5. Why `git status` Fails

Running:

```bash
git --git-dir=/opt/beta.git status
```

may return:

```text
fatal: this operation must be run in a work tree
```

This is expected.

`git status` compares the working directory, staging area, and current commit. A bare repository has **no working tree**, so there is nothing for `git status` to inspect.

---

## Normal vs Bare Repository

### Normal Repository

```text
project/
├── file1.txt
├── file2.txt
└── .git/
```

Used for editing files, staging changes, and creating commits.

### Bare Repository

```text
/opt/beta.git/
├── HEAD
├── config
├── objects/
├── refs/
└── hooks/
```

Used primarily as a shared remote repository.

---

## Example Usage

A user can later clone the bare repository:

```bash
git clone <server>:/opt/beta.git
```

Then work normally:

```bash
cd beta
git add .
git commit -m "Initial commit"
git push
```

The cloned repository contains the working tree, while `/opt/beta.git` remains the central remote.

---

## Ownership Note

Because the repository was created with `sudo`, it may be owned by `root`:

```bash
ls -ld /opt/beta.git
```

If another user later needs write access, permissions or ownership may need adjustment:

```bash
sudo chown -R <user>:<group> /opt/beta.git
```

Only change ownership if the task requires it.

---

## Useful Commands

```bash
sudo dnf install -y git
sudo git init --bare /opt/beta.git
ls -ld /opt/beta.git
ls /opt/beta.git
git --git-dir=/opt/beta.git rev-parse --is-bare-repository
```

---

## Key Takeaways

1. `git init --bare` creates a repository without a working tree.
2. Bare repositories are commonly used as shared Git remotes.
3. Commands such as `git status` require a working tree and therefore fail on bare repositories.
4. `git rev-parse --is-bare-repository` is a useful verification command.
5. The repository itself contains `HEAD`, `objects`, `refs`, and other Git metadata.

---

## Final Result

The required repository was successfully created at:

```text
/opt/beta.git
```

with:

```bash
sudo git init --bare /opt/beta.git
```
