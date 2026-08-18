# Essential Git Commands

This file contains commonly used Git commands and examples.

---

## `git init`

Initialises a new Git repository.

```bash
git init
```

This creates a `.git` directory in the current directory.

Example:

```bash
mkdir my-project
cd my-project
git init
```

---

## `git status`

Shows the current state of the repository.

```bash
git status
```

It can show:

* Modified files
* Untracked files
* Staged changes
* Current branch
* Changes ready to commit

Example:

```bash
git status
```

---

## `git add`

Stages changes for the next commit.

Stage a specific file:

```bash
git add script.sh
```

Stage multiple files:

```bash
git add file1.txt file2.txt
```

Stage everything in the current directory:

```bash
git add .
```

> Be careful with `git add .` because it stages all relevant changes in the current directory.

---

## `git commit`

Creates a commit from the staged changes.

```bash
git commit -m "Add system health check"
```

A good commit message should describe what the commit does.

Good:

```text
Add CPU monitoring function
```

Less useful:

```text
Update stuff
```

---

# File Operations

## `git rm`

Removes a file and stages the deletion.

```bash
git rm file.txt
```

This is effectively:

```text
Delete file
+
Stage deletion
```

If you already deleted the file manually, you can stage the deletion with:

```bash
git add file.txt
```

or:

```bash
git add -u
```

---

## `git mv`

Renames or moves a file while staging the change.

```bash
git mv old-name.txt new-name.txt
```

Move a file:

```bash
git mv script.sh scripts/script.sh
```

---

## `git restore`

Restores files to a previous state.

Discard unstaged changes:

```bash
git restore file.txt
```

This restores the file to its staged/HEAD state.

### Unstage a file

```bash
git restore --staged file.txt
```

This removes the file from the staging area without deleting your changes.

---

# Viewing History

## `git log`

Displays commit history.

```bash
git log
```

A shorter version:

```bash
git log --oneline
```

Visualise branches:

```bash
git log --oneline --graph --all
```

Example:

```text
* a83f92d Add feature
* 91b7c4e Update README
* 73d1a22 Initial commit
```

---

## `git show`

Displays information about a specific commit.

```bash
git show <commit>
```

Example:

```bash
git show a83f92d
```

It can show:

* Commit information
* Author
* Commit message
* Changes introduced by the commit

---

# Comparing Changes

## `git diff`

Shows unstaged changes.

```bash
git diff
```

This compares your working directory with the staging area.

---

## `git diff --staged`

Shows changes that have been staged but not committed.

```bash
git diff --staged
```

This is useful before committing:

```bash
git add script.sh
git diff --staged
git commit -m "Update script"
```

---

# `git blame`

Shows which commit and author last modified each line of a file.

```bash
git blame script.sh
```

Example:

```text
a83f92d (Lina 2026-08-18) echo "Hello"
91b7c4e (Lina 2026-08-17) uptime
```

Useful when investigating:

* Who introduced a change?
* When was a line changed?
* Which commit introduced it?

---

# Git Configuration

## Set username

```bash
git config --global user.name "Your Name"
```

## Set email

```bash
git config --global user.email "email@example.com"
```

These values are used as the author identity for your commits.

View configuration:

```bash
git config --global --list
```

View a specific setting:

```bash
git config --global user.name
```

---

# Useful Command Sequence

A common workflow is:

```bash
git status
git add .
git diff --staged
git commit -m "Describe changes"
git log --oneline
```

The important distinction is:

```text
git diff
      ↓
Unstaged changes

git diff --staged
      ↓
Staged changes
```
