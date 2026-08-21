# Branching and Merging

Branches allow developers to work on different features or changes independently without immediately affecting the main branch.

---

# What is a Branch?

A branch is essentially a **movable pointer to a commit**.

For example:

```text
A---B---C
        ↑
       main
```

Create a new branch:

```text
A---B---C
        ↑
       main
        \
         D---E
             ↑
           feature
```

This allows work on `feature` without changing `main`.

---

# `git branch`

List branches:

```bash
git branch
```

Example:

```text
* main
  feature
```

The `*` indicates the current branch.

Create a branch:

```bash
git branch feature
```

Delete a branch:

```bash
git branch -d feature
```

Force deletion:

```bash
git branch -D feature
```

> `-D` should be used carefully because it can delete a branch containing unmerged work.

---

# `git switch`

Switch to an existing branch:

```bash
git switch feature
```

Create and switch to a new branch:

```bash
git switch -c feature
```

This is the modern command for branch switching.

---

# `git checkout`

Older Git syntax:

```bash
git checkout feature
```

Create and switch:

```bash
git checkout -b feature
```

`git checkout` is still widely used, but Git introduced `git switch` to make branch operations clearer.

---

# Creating a Feature Branch

A common workflow is:

```bash
git switch main
git pull
git switch -c feature/new-feature
```

Then make changes:

```bash
git add .
git commit -m "Add new feature"
```

---

# Merging

To merge a branch into the current branch:

```bash
git switch main
git merge feature
```

Git combines the history of the two branches.

---

# Fast-Forward Merge

A fast-forward merge occurs when the target branch has not diverged.

Example:

```text
A---B
     \
      C---D
```

If `main` is still pointing at `B`, Git can simply move the `main` pointer forward:

```text
A---B---C---D
             ↑
            main
```

No merge commit is required.

---

# Three-Way Merge / Merge Commit

If both branches contain new commits, their histories have diverged.

```text
      C---D
     /
A---B
     \
      E---F
```

When the branches are merged, Git may create a new merge commit:

```text
      C---D
     /     \
A---B       M
     \     /
      E---F
```

The merge commit records that the two lines of development were combined.

---

# Merge Conflicts

A merge conflict occurs when Git cannot automatically determine which changes should be kept.

For example, two branches modify the same part of a file differently.

Git may produce:

```text
<<<<<<< HEAD
Change from current branch
=======
Change from feature branch
>>>>>>> feature
```

These are called **conflict markers**.

---

# Conflict Markers

```text
<<<<<<< HEAD
```

Marks the beginning of the current branch's version.

```text
=======
```

Separates the two versions.

```text
>>>>>>> feature
```

Marks the end of the incoming branch's version.

---

# Resolving a Conflict

### 1. Identify conflicted files

```bash
git status
```

### 2. Open the conflicted file

Decide which version should remain.

For example, change:

```text
<<<<<<< HEAD
Hello from main
=======
Hello from feature
>>>>>>> feature
```

into:

```text
Hello from feature
```

### 3. Stage the resolved file

```bash
git add file.txt
```

### 4. Complete the merge

```bash
git commit
```

### 5. Check the history

```bash
git log --oneline --graph --all
```

---

# Practical Branching Workflow

A typical feature workflow:

```bash
# Start from an updated main branch
git switch main
git pull

# Create feature branch
git switch -c feature/new-feature

# Work on feature
git add .
git commit -m "Add new feature"

# Push branch
git push -u origin feature/new-feature

# Create Pull Request on GitHub
```

After review, the feature can be merged into `main`.

---

# Branching Best Practices

### Keep `main` stable

Avoid making experimental changes directly on `main`.

### Use descriptive branch names

Examples:

```text
feature/login
feature/system-monitor
fix/memory-calculation
docs/git-notes
```

### Make focused commits

Prefer:

```text
Add CPU monitoring function
```

over:

```text
Update everything
```

### Keep branches reasonably short-lived

Long-running branches are more likely to develop conflicts with the main branch.
