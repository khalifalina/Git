# Git Security and Best Practices

Git repositories can contain sensitive information if they are not managed carefully.

A major principle is:

> **Never commit secrets to Git.**

---

# `.gitignore`

A `.gitignore` file tells Git which files should not be tracked.

Create one:

```bash
touch .gitignore
```

Example:

```gitignore
*.log
.env
node_modules/
__pycache__/
*.pyc
.DS_Store
```

---

# What Should Be Ignored?

Common examples include:

### Environment files

```text
.env
.env.local
```

These may contain credentials or configuration.

### Dependencies

```text
node_modules/
```

### Logs

```text
*.log
```

### Python cache

```text
__pycache__/
*.pyc
```

### Operating system files

```text
.DS_Store
Thumbs.db
```

---

# What Should Never Be Committed?

Never commit sensitive information such as:

* Passwords
* API keys
* Access tokens
* Cloud credentials
* Database credentials
* Private SSH keys
* Private certificates
* Secrets stored in `.env` files

For example:

```text
API_KEY=123456789
PASSWORD=mysecretpassword
```

should never be committed to a public repository.

---

# `.env` Files

Environment files are often used to store configuration:

```text
API_KEY=secret
DATABASE_URL=...
```

Add them to `.gitignore`:

```gitignore
.env
.env.*
```

Instead of committing secrets, provide a safe example file:

```text
.env.example
```

For example:

```text
API_KEY=
DATABASE_URL=
```

This shows developers which variables they need without exposing the actual credentials.

---

# SSH Private Keys

Your private SSH key must remain private.

For example:

```text
~/.ssh/id_ed25519
```

should **never** be uploaded to GitHub.

The public key:

```text
~/.ssh/id_ed25519.pub
```

can be added to GitHub.

Remember:

```text
Private key → SECRET
Public key  → Safe to share
```

---

# API Keys and Passwords

Do not hard-code credentials in source code.

Bad:

```python
API_KEY = "abc123secret"
```

Better:

```python
import os

API_KEY = os.getenv("API_KEY")
```

The actual key can then be provided through an environment variable.

---

# Secret Scanning

Secret scanning tools look for credentials and sensitive information before or after code is committed.

Examples include:

* GitHub secret scanning
* `git-secrets`
* TruffleHog
* Pre-commit hooks

These tools can help detect accidentally committed secrets.

---

# `git-secrets`

`git-secrets` is designed to help prevent secrets from being committed.

The general idea is:

```text
Developer
    ↓
git commit
    ↓
Secret scanning
    ↓
Potential secret?
   / \
 Yes  No
 ↓     ↓
Block  Commit
```

This adds an additional layer of protection.

---

# TruffleHog

TruffleHog is a security tool designed to detect secrets in repositories and their history.

It can search for things such as:

* API keys
* Credentials
* Tokens
* High-entropy secrets

Secret scanning is particularly important because deleting a secret from the latest version of a file does not necessarily remove it from Git history.

---

# Pre-Commit Hooks

Git hooks allow scripts to run automatically at certain points in the Git workflow.

A **pre-commit hook** runs before a commit is created.

It can be used for:

* Formatting
* Linting
* Testing
* Secret detection
* Code quality checks

---

# Pre-Commit Framework

The `pre-commit` framework allows repositories to define automated checks in:

```text
.pre-commit-config.yaml
```

A configuration might define several hooks:

```yaml
repos:
  - repo: ...
    hooks:
      - id: ...
```

The exact hooks depend on the project.

The basic workflow is:

```text
git commit
     ↓
Pre-commit hooks
     ↓
Checks pass?
   /      \
 Yes       No
 ↓         ↓
Commit    Fix issues
```

---

# What If a Secret Is Accidentally Committed?

Simply deleting the secret from the file is **not enough**.

The secret may still exist in previous commits.

If a secret has been exposed:

### 1. Revoke or rotate it immediately

For example, invalidate the API key or change the password.

### 2. Remove the secret from the repository

Remove it from the current files.

### 3. Consider the Git history

If necessary, rewrite the repository history using appropriate Git history-rewriting tools.

### 4. Check for further exposure

Search the repository and its history for the leaked credential.

### 5. Treat the secret as compromised

Even if the repository was private, assume the credential may have been exposed if it was committed or pushed unintentionally.

---

# Important Security Principle

`.gitignore` helps **prevent files from being tracked**, but it is not a security mechanism by itself.

For example:

```text
.env
```

in `.gitignore` does not remove an `.env` file that was already committed.

If a file is already tracked, you may need to remove it from tracking:

```bash
git rm --cached .env
```

Then commit the change.

---

# Git Best Practices

### 1. Never commit secrets

Use environment variables or secret-management systems.

### 2. Use `.gitignore`

Prevent unnecessary or sensitive files from entering the repository.

### 3. Write meaningful commit messages

```text
Add memory monitoring function
```

is preferable to:

```text
Update
```

### 4. Keep commits focused

Each commit should represent a logical change.

### 5. Use branches

Develop features and fixes separately from `main`.

### 6. Review before committing

```bash
git status
git diff
```

After staging:

```bash
git diff --staged
```

### 7. Use Pull Requests

Review changes before merging into important branches.

### 8. Use automated checks

Pre-commit hooks and CI pipelines can catch problems early.

---

# Security Checklist

Before pushing code:

```text
[ ] No passwords committed
[ ] No API keys committed
[ ] No SSH private keys committed
[ ] No credentials in .env files
[ ] .gitignore is configured
[ ] Changes have been reviewed
[ ] git diff has been checked
[ ] Secrets have been scanned
```

The goal is to make security part of the normal Git workflow rather than something checked only after a problem occurs.
