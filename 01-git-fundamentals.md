# Git Fundamentals

## What is Git?

Git is a **distributed version control system (VCS)** used to track changes to files and manage different versions of a project.

Git allows you to:

* Track changes over time
* See who made changes and when
* Revert or recover changes
* Create separate branches for different features
* Merge work from different branches
* Collaborate with other developers

Because Git is distributed, each developer normally has a complete copy of the repository and its history on their local machine.

---

## Git vs GitHub

Git and GitHub are related, but they are not the same thing.

### Git

Git is the **version control software**.

It runs locally on your computer and manages your repository, commits, branches, and history.

### GitHub

GitHub is a **remote hosting and collaboration platform** built around Git.

It provides features such as:

* Remote repositories
* Pull Requests
* Code reviews
* Issues
* Project management
* Collaboration

A useful way to think about it:

```text
Git
│
├── Tracks versions
├── Creates commits
├── Creates branches
└── Manages local history

GitHub
│
├── Hosts Git repositories
├── Pull Requests
├── Code reviews
└── Collaboration
```

---

# Snapshots vs Diffs

Git is often described as a system that tracks changes, but internally Git primarily thinks in terms of **snapshots**.

A commit represents the state of the project at a particular point in time.

```text
Commit A
   │
   ▼
Snapshot of project

Commit B
   │
   ▼
New snapshot of project

Commit C
   │
   ▼
New snapshot of project
```

Git can efficiently reuse data that has not changed rather than storing completely independent copies of every file.

### Diffs

A diff describes the differences between two versions.

For example:

```text
Version 1:
Hello

Version 2:
Hello World
```

The diff would represent the addition of:

```text
+ World
```

Git can calculate these differences when displaying changes, but its underlying model is based around snapshots and objects.

---

# SHA-1 Hashes

Git uses **SHA-1 hashes** to identify objects.

A SHA-1 hash is a 40-character hexadecimal value.

Example:

```text
e3b0c44298fc1c149afbf4c8996fb924...
```

Commits, trees, and blobs are identified by hashes.

You will often see shortened commit hashes when looking at history:

```bash
git log --oneline
```

Example:

```text
a83f92d Add login functionality
91b7c4e Update README
```

The shortened hash can be used to reference the commit:

```bash
git show a83f92d
```

> Modern Git can also use SHA-256 repositories, but SHA-1 remains the traditional and common object format you'll encounter.

---

# Git Objects

Git stores information as objects.

The three important objects for understanding Git are:

```text
Blob
 │
 └── File contents

Tree
 │
 └── Directory structure

Commit
 │
 └── Snapshot + metadata
```

---

## Blobs

A **blob** stores the contents of a file.

It does not store the filename itself.

For example:

```text
hello.txt
```

containing:

```text
Hello World
```

Git stores the file's contents as a blob.

The filename and location are handled by a tree.

---

## Trees

A **tree** represents a directory.

It connects filenames with the objects containing their data.

Conceptually:

```text
Tree
├── hello.txt → Blob
├── README.md → Blob
└── src/ → Tree
```

Trees allow Git to represent the structure of a project.

---

## Commits

A **commit** represents a snapshot of the project along with metadata.

A commit contains information such as:

* Author
* Committer
* Commit message
* Parent commit
* Root tree

Conceptually:

```text
Commit
│
├── Author
├── Message
├── Parent
└── Tree
     ├── README.md → Blob
     ├── script.sh → Blob
     └── src/ → Tree
```

This object structure allows Git to build the project's history.

---

# The `.git` Directory

When you run:

```bash
git init
```

Git creates a hidden `.git` directory.

```text
project/
├── .git/
├── file1.txt
└── file2.txt
```

The `.git` directory contains the information Git needs to manage the repository.

Important components include:

```text
.git/
├── HEAD
├── config
├── index
├── objects/
├── refs/
└── ...
```

> The `.git` directory is extremely important. Deleting it removes the repository's Git metadata and history, although your working files remain.

---

# HEAD

`HEAD` is a reference to your current location in the Git history.

Usually, `HEAD` points to the current branch.

For example:

```text
HEAD
 ↓
main
 ↓
Commit C
```

You can see the current branch with:

```bash
git branch
```

You can also inspect `HEAD`:

```bash
cat .git/HEAD
```

You may see something similar to:

```text
ref: refs/heads/main
```

---

# refs

`refs` contains references to commits.

Branches are stored under:

```text
.git/refs/heads/
```

For example:

```text
.git/
└── refs/
    └── heads/
        ├── main
        └── feature
```

A branch is essentially a movable reference pointing to a commit.

```text
main
 ↓
C3
```

When a new commit is created:

```text
main
 ↓
C4
```

The branch reference moves forward.

---

# objects

Git objects are stored inside:

```text
.git/objects/
```

This is where Git stores objects such as:

* Blobs
* Trees
* Commits

You generally should not modify this directory manually.

---

# config

The repository's configuration is stored in:

```text
.git/config
```

For example, a repository may contain information about its remote:

```text
[remote "origin"]
    url = git@github.com:username/repository.git
```

Global Git configuration is stored separately.

You can view it using:

```bash
git config --global --list
```

---

# index

The `index` represents the **staging area**.

When you run:

```bash
git add file.txt
```

Git updates the index with the version of the file you want included in the next commit.

The index is located at:

```text
.git/index
```

---

# The Three Main Areas of Git

The basic Git workflow consists of three main areas:

```text
Working Directory
       │
       │ git add
       ▼
Staging Area
       │
       │ git commit
       ▼
Repository
```

### Working Directory

The files you are currently working on.

### Staging Area

The changes you have selected for the next commit.

### Repository

The committed history stored by Git.

---

# What Happens When You Run `git add`?

Suppose you modify:

```text
script.sh
```

The change initially exists only in your working directory.

```text
Working Directory
       │
       │ git add script.sh
       ▼
Staging Area
```

When you run:

```bash
git add script.sh
```

Git:

1. Examines the changed file.
2. Creates or identifies the necessary blob object.
3. Updates the staging area/index.
4. Records that this version of the file should be included in the next commit.

The change has **not been committed yet**.

You can check the state with:

```bash
git status
```

---

# What Happens When You Run `git commit`?

When you run:

```bash
git commit -m "Update script"
```

Git uses the contents of the staging area to create a new snapshot.

Conceptually:

```text
Working Directory
       │
       │ git add
       ▼
Staging Area
       │
       │ git commit
       ▼
New Commit
```

The commit contains information about the snapshot, including its tree and parent commit.

The current branch then moves to the new commit.

```text
Before:

main
 ↓
C2


After:

main
 ↓
C3
```

The previous commit still exists as part of the history.

---

# The Complete Workflow

A typical local workflow is:

```bash
# Make changes
nano script.sh

# Check changes
git status

# Stage changes
git add script.sh

# Review staged changes
git diff --staged

# Commit
git commit -m "Update script"

# View history
git log --oneline
```

The important concept to remember is:

```text
EDIT
 ↓
Working Directory
 ↓
git add
 ↓
Staging Area
 ↓
git commit
 ↓
Repository
```

This separation allows you to choose **exactly which changes should be included in a commit**.
