# Advanced Git

This section covers Git commands used for managing unfinished work, moving commits between branches, and rewriting history.

---

# `git stash`

`git stash` temporarily saves uncommitted changes.

This is useful when you need to switch branches but are not ready to commit your current work.

Example:

```bash
git stash
```

Your working directory becomes clean.

You can then switch branches:

```bash
git switch main
```

---

# `git stash pop`

Restore the most recently stashed changes:

```bash
git stash pop
```

This restores the changes and removes the stash from the stash list.

View stashes:

```bash
git stash list
```

Example:

```text
stash@{0}: WIP on feature/login
stash@{1}: WIP on main
```

---

# `git cherry-pick`

Cherry-pick applies a specific commit to the current branch.

```bash
git cherry-pick <commit>
```

Example:

```bash
git cherry-pick a83f92d
```

This is useful when you need one particular change from another branch without merging the entire branch.

---

# Merge vs Rebase

Both `merge` and `rebase` combine work from different lines of development, but they do it differently.

## Merge

```bash
git merge feature
```

Preserves the existing history.

```text
      C---D
     /     \
A---B       M
     \     /
      E---F
```

Advantages:

* Preserves branch history
* Does not rewrite existing commits
* Safer for shared branches

---

## Rebase

```bash
git rebase main
```

Replays your commits on top of another branch.

Before:

```text
      C---D
     /
A---B---E---F
```

After rebasing:

```text
A---B---E'---F'
         \
          C'---D'
```

The commits receive new identities because their history has changed.

Advantages:

* Cleaner, linear history
* Can make project history easier to read

---

# Important Rebase Rule

> **Do not casually rebase commits that other people are already working from.**

Rebase rewrites commit history.

If you rewrite a shared branch, other developers may have to reconcile their local history with the rewritten version.

A common rule is:

```text
Private/local branch → Rebase is usually fine

Shared/public branch → Prefer merge
```

---

# Interactive Rebase

Interactive rebase allows you to modify a series of commits.

For the last three commits:

```bash
git rebase -i HEAD~3
```

Git opens an editor containing something similar to:

```text
pick a83f92d Add feature
pick 91b7c4e Update feature
pick 73d1a22 Fix typo
```

You can change the commands.

---

# Squashing Commits

Squashing combines multiple commits into one.

Example:

```text
pick a83f92d Add feature
squash 91b7c4e Fix feature
squash 73d1a22 Fix typo
```

This can turn several small commits into a cleaner commit:

```text
Add feature
```

Useful before creating a Pull Request when your branch contains many small development commits.

---

# Reordering Commits

Interactive rebase also allows commits to be reordered.

For example:

```text
pick A
pick B
pick C
```

can become:

```text
pick B
pick A
pick C
```

Git will attempt to replay them in the new order.

However, changing commit order can cause conflicts if later commits depend on earlier ones.

---

# Editing Commits

Change:

```text
pick
```

to:

```text
edit
```

Git pauses at that commit.

You can then modify the commit and continue the rebase.

For example:

```bash
git commit --amend
git rebase --continue
```

---

# When Not to Rebase

Avoid rebasing:

* Shared branches
* Public branches other developers are using
* History that others have already based work on

Rebase is most useful for **cleaning up your own local history before sharing it**.

---

# Advanced Workflow Example

```bash
# Create feature branch
git switch -c feature/example

# Make several commits
git add .
git commit -m "Start feature"

git add .
git commit -m "Fix feature"

git add .
git commit -m "Fix typo"

# Clean up commits
git rebase -i HEAD~3
```

After cleaning up the history:

```bash
git push -u origin feature/example
```

If the branch has already been pushed and rebased, a force push may be required:

```bash
git push --force-with-lease
```

`--force-with-lease` is generally safer than `--force` because Git checks that the remote branch has not unexpectedly changed.
