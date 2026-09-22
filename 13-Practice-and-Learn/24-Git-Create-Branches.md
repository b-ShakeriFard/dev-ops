# Git Create Branches

## Objective

Create a new Git branch from the `master` branch inside:

    /usr/src/kodekloudrepos/apps

------------------------------------------------------------------------

## Steps

### 1. SSH into storage server

``` bash
ssh natasha@ststor01
```

### 2. Navigate to repository

``` bash
cd /usr/src/kodekloudrepos/apps
```

### 3. Fix Git safe directory warning

If Git reports:

    fatal: detected dubious ownership

Trust the repository:

``` bash
git config --global --add safe.directory /usr/src/kodekloudrepos/apps
```

### 4. Switch to master

``` bash
git checkout master
```

### 5. Create a new branch

``` bash
git checkout -b xfusioncorp_apps
```

### 6. Verify

``` bash
git branch
```

Example:

      master
    * xfusioncorp_apps

The `*` shows the active branch.

------------------------------------------------------------------------

# Key Concepts

## Git Branch

A branch is a movable pointer to a commit history. It allows developers
to work on changes independently.

Example:

    master
      |
      +---- xfusioncorp_apps

------------------------------------------------------------------------

## Useful Commands

  Command                  Purpose
  ------------------------ -------------------------------
  `git branch`             List branches
  `git checkout branch`    Switch branches
  `git checkout -b name`   Create and switch to a branch
  `git branch -d name`     Delete a branch
  `git status`             Show repository state

------------------------------------------------------------------------

## Troubleshooting

### Dubious Ownership

Git protects repositories owned by another user.

Fix:

``` bash
git config --global --add safe.directory <repository-path>
```

------------------------------------------------------------------------

## Summary

Successfully created:

    master → xfusioncorp_apps

This workflow is commonly used for feature development and isolated
changes in DevOps teams.
