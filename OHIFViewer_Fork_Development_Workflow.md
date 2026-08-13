# OHIFViewer Fork Development & Synchronization Guide

## 1. Purpose

This document explains the recommended Git workflow for the
`CHAVI-India/OHIFViewer` fork of the upstream `OHIF/Viewers` repository.

The goal is to make it safe and predictable for any developer to:

1.  Get the latest changes from the upstream OHIF repository.
2.  Synchronize the fork's `master` branch.
3.  Create a development branch.
4.  Make and commit changes.
5.  Push the development branch to the CHAVI fork.
6.  Create and merge a Pull Request into the fork's `master`.
7.  Continue working without accidentally pushing anything to the
    upstream OHIF repository.

------------------------------------------------------------------------

# 2. Repository Structure

There are two repositories involved.

  --------------------------------------------------------------------------
  Remote                  Repository                 Purpose
  ----------------------- -------------------------- -----------------------
  `upstream`              `OHIF/Viewers`             Original/parent OHIF
                                                     repository

  `origin`                `CHAVI-India/OHIFViewer`   Our fork where CHAVI
                                                     changes are maintained
  --------------------------------------------------------------------------

The expected Git configuration is:

``` bash
git remote -v
```

Expected result:

``` text
origin    git@github.com:CHAVI-India/OHIFViewer.git
upstream  https://github.com/OHIF/Viewers.git
```

## Important Rule

### `upstream`

Used to:

-   Fetch the latest OHIF changes.
-   Keep our fork synchronized with the original OHIF project.

We **do not push to `upstream`**.

### `origin`

Used to:

-   Push our branches.
-   Push the fork's `master`.
-   Maintain CHAVI-specific changes.

We **push only to `origin`**.

------------------------------------------------------------------------

# 3. Branch Strategy

The repository should follow this structure:

``` text
OHIF/Viewers
      |
      | fetch/merge
      v
CHAVI-India/OHIFViewer
      |
      +---- master
      |       |
      |       +---- Latest upstream OHIF
      |       +---- Approved CHAVI changes
      |
      +---- fix/*
      |
      +---- feature/*
      |
      +---- chore/*
```

## Meaning of the branches

### `master`

The main branch of our fork.

It should contain:

-   Latest synchronized upstream OHIF code.
-   CHAVI changes that have already been reviewed and merged.

Developers should normally **not make direct changes on `master`**.

### Development branches

Examples:

``` text
fix/chavi-datasource
feature/new-dicom-loader
fix/viewer-performance
chore/update-dependencies
```

All new work should be done on a development branch and merged into
`master` through a Pull Request.

------------------------------------------------------------------------

# 4. First-Time Repository Setup

A new developer should clone the CHAVI fork:

``` bash
git clone git@github.com:CHAVI-India/OHIFViewer.git
cd OHIFViewer
```

Add the upstream OHIF repository:

``` bash
git remote add upstream https://github.com/OHIF/Viewers.git
```

Verify:

``` bash
git remote -v
```

Expected:

``` text
origin    git@github.com:CHAVI-India/OHIFViewer.git
origin    git@github.com:CHAVI-India/OHIFViewer.git
upstream  https://github.com/OHIF/Viewers.git
upstream  https://github.com/OHIF/Viewers.git
```

------------------------------------------------------------------------

# 5. Before Starting Any New Development

Always start by synchronizing `master`.

Why?

The parent OHIF repository may have received:

-   Bug fixes
-   Security updates
-   Dependency updates
-   New features
-   Version changes
-   Performance improvements

If you create a branch from an outdated `master`, your work may later
conflict with upstream changes.

## Step 1: Switch to master

``` bash
git checkout master
```

## Step 2: Fetch upstream

``` bash
git fetch upstream
```

`fetch` downloads the latest upstream information but does not modify
your working files.

## Step 3: Update local master

``` bash
git merge upstream/master
```

This incorporates the latest upstream changes into our local `master`.

## Step 4: Push synchronized master to our fork

``` bash
git push origin master
```

At this point:

``` text
OHIF/Viewers/master
        |
        v
local master
        |
        v
CHAVI-India/OHIFViewer/master
```

This is important because our fork's `master` should represent the
latest upstream baseline plus approved CHAVI changes.

------------------------------------------------------------------------

# 6. Create a Development Branch

Never start new work directly on `master`.

Create a branch from the updated `master`.

Example:

``` bash
git checkout -b fix/chavi-dicom-loading
```

Or:

``` bash
git checkout -b feature/new-datasource
```

Verify:

``` bash
git branch
```

You should see:

``` text
* fix/chavi-dicom-loading
  master
```

------------------------------------------------------------------------

# 7. Make Your Changes

Now make the required code/configuration changes.

For example:

``` text
extensions/default/
platform/app/
platform/core/
```

Before committing, inspect the changes:

``` bash
git status
```

Review the actual changes:

``` bash
git diff
```

This is important because it prevents accidentally committing:

-   Debug files
-   Temporary changes
-   Secrets
-   Unrelated modifications
-   Generated files that should not be committed

------------------------------------------------------------------------

# 8. Commit Your Changes

Stage the intended files:

``` bash
git add .
```

Check what will be committed:

``` bash
git status
```

Then commit using a meaningful Conventional Commit message.

Examples:

``` bash
git commit -m "feat: integrate Chavi datasource for DICOM study fetching"
```

``` bash
git commit -m "fix: handle missing DICOM study metadata"
```

``` bash
git commit -m "chore: update OHIF dependencies"
```

Recommended prefixes:

  Prefix        Use
  ------------- -------------------------
  `feat:`       New functionality
  `fix:`        Bug fix
  `chore:`      Maintenance
  `refactor:`   Code restructuring
  `perf:`       Performance improvement
  `docs:`       Documentation
  `test:`       Tests

------------------------------------------------------------------------

# 9. Push the Development Branch to Our Fork

Push the branch to `origin`:

``` bash
git push -u origin fix/chavi-dicom-loading
```

This creates/updates the branch in:

``` text
CHAVI-India/OHIFViewer
```

It does **not** push anything to:

``` text
OHIF/Viewers
```

The `-u` option establishes the upstream tracking relationship between
the local branch and the remote branch.

After the first push, future pushes can usually be:

``` bash
git push
```

------------------------------------------------------------------------

# 10. Create a Pull Request

After pushing, create a Pull Request on GitHub.

The PR must be:

``` text
Repository:
CHAVI-India/OHIFViewer

Base:
master

Compare:
fix/chavi-dicom-loading
```

The direction should be:

``` text
fix/chavi-dicom-loading
            |
            v
        CHAVI master
```

## Important

Do NOT accidentally create:

``` text
fix/chavi-dicom-loading
            |
            v
OHIF/Viewers/master
```

unless you intentionally want to contribute the change to the upstream
OHIF project.

For normal CHAVI development, the PR should stay entirely inside our
fork.

------------------------------------------------------------------------

# 11. Review and Merge

The PR should be reviewed before merging.

Review:

-   Functional changes
-   Security
-   Configuration
-   Environment variables
-   Dependency changes
-   Tests
-   Build
-   Potential impact on existing OHIF functionality

After approval, merge the PR into:

``` text
CHAVI-India/OHIFViewer/master
```

Do not merge it directly into the upstream `OHIF/Viewers` repository.

------------------------------------------------------------------------

# 12. After the Pull Request Is Merged

Update your local `master`:

``` bash
git checkout master
git pull origin master
```

The local `master` now contains the merged CHAVI changes.

Optionally delete the local development branch:

``` bash
git branch -d fix/chavi-dicom-loading
```

If the remote branch should also be deleted:

``` bash
git push origin --delete fix/chavi-dicom-loading
```

Deleting feature branches is optional, but keeping the repository clean
is recommended.

------------------------------------------------------------------------

# 13. What If OHIF Gets New Changes While You Are Working?

This is a common situation.

Suppose you created:

``` text
fix/my-feature
```

and while you were developing, `OHIF/Viewers/master` received new
commits.

Do not blindly pull from random branches.

First update our fork's `master`.

``` bash
git checkout master
git fetch upstream
git merge upstream/master
git push origin master
```

Now our fork's `master` contains the latest upstream changes.

Then update your development branch.

``` bash
git checkout fix/my-feature
git merge master
```

If conflicts occur, resolve them carefully, test the application, then
commit the merge resolution.

Finally:

``` bash
git push
```

------------------------------------------------------------------------

# 14. Recommended Daily Workflow

For most development work, follow this sequence.

## Start work

``` bash
git checkout master
git fetch upstream
git merge upstream/master
git push origin master
```

## Create a branch

``` bash
git checkout -b fix/my-change
```

## Develop

Make your changes.

## Review

``` bash
git status
git diff
```

## Commit

``` bash
git add .
git commit -m "fix: describe the change"
```

## Push

``` bash
git push -u origin fix/my-change
```

## Pull Request

Create:

``` text
CHAVI-India/OHIFViewer

fix/my-change -> master
```

## Merge

Merge after review.

## Clean local repository

``` bash
git checkout master
git pull origin master
git branch -d fix/my-change
```

------------------------------------------------------------------------

# 15. Important: Do Not Use `git pull` Blindly

Avoid doing this on `master` without understanding the current state:

``` bash
git pull
```

Why?

Your local branch may be behind or have diverged from the remote.

Instead, use the explicit workflow:

``` bash
git checkout master
git fetch upstream
git merge upstream/master
git push origin master
```

This makes it clear that:

``` text
upstream/master
```

is the source of the latest OHIF changes.

------------------------------------------------------------------------

# 16. Check Branch Status Before Important Operations

Useful commands:

## Current branch

``` bash
git branch --show-current
```

## Working tree

``` bash
git status
```

## Remotes

``` bash
git remote -v
```

## Recent commits

``` bash
git log --oneline --decorate -10
```

## Visual history

``` bash
git log --oneline --graph --decorate --all -20
```

------------------------------------------------------------------------

# 17. Verify Whether Your Branch Is Behind Upstream

For a development branch:

``` bash
git fetch upstream
git log --oneline HEAD..upstream/master
```

If there is no output:

``` text
```

your branch contains all commits currently in `upstream/master`.

If commits are shown, your branch is behind upstream.

To see commits that exist in your branch but not upstream:

``` bash
git log --oneline upstream/master..HEAD
```

------------------------------------------------------------------------

# 18. Handling Merge Conflicts

Conflicts can happen when both CHAVI and OHIF modify the same code.

If you run:

``` bash
git merge upstream/master
```

and Git reports conflicts:

``` text
CONFLICT (content): Merge conflict in ...
```

Do not panic.

Run:

``` bash
git status
```

Git will show the conflicting files.

Open each file and resolve the conflict.

You may see:

``` text
<<<<<<< HEAD
CHAVI changes
=======
OHIF upstream changes
>>>>>>> upstream/master
```

Decide which implementation should remain, or combine both.

After resolving:

``` bash
git add <resolved-file>
```

Then complete the merge:

``` bash
git commit
```

Run the project's tests/build before pushing:

``` bash
git push
```

If the conflict is complicated and you are unsure, stop and review the
changes before continuing.

------------------------------------------------------------------------

# 19. Never Push Directly to Upstream

This is the most important safety rule.

Do NOT run:

``` bash
git push upstream master
```

Do NOT run:

``` bash
git push upstream fix/my-change
```

For CHAVI development, pushes should go to:

``` bash
git push origin master
```

or:

``` bash
git push origin fix/my-change
```

Remember:

``` text
origin   = CHAVI-India/OHIFViewer
upstream = OHIF/Viewers
```

------------------------------------------------------------------------

# 20. Recommended Authentication

SSH is recommended.

Test SSH authentication:

``` bash
ssh -T git@github.com
```

A successful response will indicate that GitHub authenticated your
account.

The `origin` URL should preferably be:

``` text
git@github.com:CHAVI-India/OHIFViewer.git
```

This avoids repeatedly entering HTTPS credentials/tokens.

------------------------------------------------------------------------

# 21. Complete Example

Suppose a developer wants to fix a DICOM loading issue.

### Step 1 --- synchronize master

``` bash
git checkout master
git fetch upstream
git merge upstream/master
git push origin master
```

### Step 2 --- create branch

``` bash
git checkout -b fix/dicom-loading
```

### Step 3 --- modify code

Make the required changes.

### Step 4 --- review

``` bash
git status
git diff
```

### Step 5 --- commit

``` bash
git add .
git commit -m "fix: improve DICOM study loading"
```

### Step 6 --- push

``` bash
git push -u origin fix/dicom-loading
```

### Step 7 --- create PR

``` text
CHAVI-India/OHIFViewer

fix/dicom-loading
        ↓
      master
```

### Step 8 --- review and merge

Merge the PR into:

``` text
CHAVI-India/OHIFViewer/master
```

### Step 9 --- update local master

``` bash
git checkout master
git pull origin master
```

### Step 10 --- clean up

``` bash
git branch -d fix/dicom-loading
```

------------------------------------------------------------------------

# 22. Quick Reference

## Synchronize fork with OHIF

``` bash
git checkout master
git fetch upstream
git merge upstream/master
git push origin master
```

## Start new work

``` bash
git checkout master
git checkout -b fix/my-change
```

## Commit

``` bash
git add .
git commit -m "fix: describe change"
```

## Push development branch

``` bash
git push -u origin fix/my-change
```

## After PR merge

``` bash
git checkout master
git pull origin master
git branch -d fix/my-change
```

## Check remotes

``` bash
git remote -v
```

## Check status

``` bash
git status
```

------------------------------------------------------------------------

# 23. Mental Model for New Developers

Always remember this simple flow:

``` text
             ORIGINAL OHIF
            OHIF/Viewers
                 |
                 | fetch
                 v
       CHAVI FORK - master
       CHAVI-India/OHIFViewer
                 |
                 | create branch
                 v
          fix/feature branch
                 |
                 | develop
                 | commit
                 | push
                 v
       CHAVI-India/OHIFViewer
          fix/feature branch
                 |
                 | Pull Request
                 v
       CHAVI-India/OHIFViewer
               master
```

The direction of responsibility is:

``` text
OHIF/Viewers
    ↓
sync
    ↓
CHAVI master
    ↓
development branch
    ↓
PR
    ↓
CHAVI master
```

This workflow keeps the fork synchronized with upstream while keeping
CHAVI-specific development isolated, reviewable, and safe.
