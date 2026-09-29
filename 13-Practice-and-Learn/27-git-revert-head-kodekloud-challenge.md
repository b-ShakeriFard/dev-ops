# KodeKloud Git Challenge: Revert the Latest Commit

## The challenge

Revert the changes introduced by the latest commit (`HEAD`) so the project files return to their state before that commit. The new commit must have this exact message:

```text
revert beta
```

The repository path and original commit hash were not provided, so substitute the location of your KodeKloud checkout and inspect its history before running the commands.

## Quick reference

```bash
cd /path/to/repository
git status --short
git log --oneline -3
git revert --no-commit HEAD
git commit -m "revert beta"
git log --oneline -3
git status --short
```

Run this sequence with a clean working tree. If `git status --short` shows changes you want to keep, save or commit them first; do not blindly discard them.

## What happens

Suppose the history starts as:

```text
B  beta changes  (HEAD)
A  previous commit
```

The revert produces a **new** commit, `C`, whose changes undo those introduced by `B`:

```text
C  revert beta  (HEAD)
B  beta changes
A  previous commit
```

The original commit remains in history. Assuming no other changes, the files at `C` match their state at `A`. The `HEAD` commit itself does **not** become `A`.

## Step-by-step guide

### 1. Enter the correct repository and inspect its state

```bash
cd /path/to/repository
git status --short
git log --oneline -3
```

Confirm that you are on the intended branch, the working tree is clean, and the commit to undo is at `HEAD`. For a fuller status check, use `git status -sb`.

### 2. Apply the inverse changes without committing yet

```bash
git revert --no-commit HEAD
```

`git revert` computes the inverse of the latest commit; `--no-commit` stages the result and lets you set the exact commit message yourself.

### 3. Create the required commit

```bash
git commit -m "revert beta"
```

Use lowercase letters and the exact spacing shown if KodeKloud checks the message.

### 4. Verify the result

```bash
git log --oneline -3
git show --stat --oneline HEAD
git status --short
```

The newest log entry should read `revert beta`, followed by the reverted commit. The working tree should normally be clean. To check whether the file tree matches the commit before the reverted one:

```bash
git diff --exit-code HEAD HEAD~2
```

With a simple single-parent commit and a clean starting state, this should produce no output and exit successfully. `HEAD~2` refers to the commit before the original `HEAD`, since the revert itself is now the latest commit.

## `revert` versus `reset`

| Command | Effect on history | Appropriate here? |
| --- | --- | --- |
| `git revert HEAD` | Adds a commit that undoes `HEAD` | Yes, with the required custom message |
| `git reset --hard HEAD~1` | Moves the branch backward and discards local changes | No; it creates no `revert beta` commit |

If you use `git revert HEAD` without `--no-commit`, Git opens an editor with a default message. You can replace that message with `revert beta`, but the two-command approach above is simpler for this challenge.

## Troubleshooting

- **Uncommitted changes block the revert:** Inspect `git status`; commit or safely set aside your work, then retry.
- **Merge conflict:** Edit the conflicted files, `git add` each resolved file, then run `git revert --continue`. If you need to abandon the attempt, run `git revert --abort`.
- **Nothing to commit:** Check `git status` and `git log`. The changes may already have been undone, or the chosen commit may have no effective inverse in the current tree. Do not create an empty commit unless the task explicitly calls for one.
- **Latest commit is a merge commit:** A merge revert needs a mainline parent, such as `git revert -m 1 HEAD`; inspect the merge and choose the parent deliberately before proceeding.

## Lessons learned

1. `HEAD` means the commit currently checked out on your branch.
2. Reverting preserves history by recording the undo as a new commit.
3. `--no-commit` lets you specify an exact message with `git commit -m`.
4. Verify both the resulting files and the new commit message before submitting the challenge.

## Interview questions

1. How does `git revert` differ from `git reset`?
2. Why is `git revert` often preferred for a commit already shared with others?
3. What does `HEAD~1` refer to before and after a revert?
4. What does `git revert --no-commit HEAD` change before `git commit` runs?
5. How do you continue or abort a revert after a conflict?
6. Why does reverting a merge commit require selecting a mainline parent?
