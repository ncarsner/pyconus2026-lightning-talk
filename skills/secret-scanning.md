# Skill: Secret Scanning & Pre-commit Hooks

Mandatory pre-commit setup for all projects derived from these templates.
No project may accept contributions without these hooks installed and passing.

---

## Quick Reference

```bash
uv add --dev pre-commit detect-secrets
pre-commit install
detect-secrets scan > .secrets.baseline
git add .pre-commit-config.yaml .secrets.baseline
pre-commit run --all-files
```

---

## Required Tools

| Tool | Purpose | Install |
|------|---------|---------|
| `pre-commit` | Hook runner | `uv add --dev pre-commit` |
| `detect-secrets` | Pattern-based secret detection | `uv add --dev detect-secrets` |

---

## Setup Steps

### 1. Install tools

```bash
uv add --dev pre-commit detect-secrets
```

### 2. Copy the hook configuration

```bash
cp templates/.pre-commit-config.yaml .pre-commit-config.yaml
```

### 3. Create the secrets baseline

Scan the existing codebase to establish a baseline of known non-secret patterns:

```bash
detect-secrets scan > .secrets.baseline
```

Commit both files before installing hooks:

```bash
git add .pre-commit-config.yaml .secrets.baseline
git commit -m "chore: add secret scanning pre-commit hooks"
```

### 4. Install hooks

```bash
pre-commit install
```

Hooks now run automatically on every `git commit`.

### 5. Verify all files pass

```bash
pre-commit run --all-files
```

---

## Hooks Included

| Hook | Source | Purpose |
|------|--------|---------|
| `detect-secrets` | Yelp/detect-secrets | Pattern-based secret detection |
| `detect-private-key` | pre-commit-hooks | RSA/EC private key detection |
| `check-added-large-files` | pre-commit-hooks | Block accidental binary commits (>500 KB) |
| `check-merge-conflict` | pre-commit-hooks | Catch unresolved merge conflict markers |

---

## Updating the Baseline

When `detect-secrets` flags a false positive:

1. Audit the flagged line — confirm it is not a real secret.
2. Update the baseline to mark it as allowed:

```bash
detect-secrets scan --baseline .secrets.baseline
```

3. Commit the updated baseline with a comment explaining the false positive.

---

## Keeping Hooks Current

```bash
pre-commit autoupdate
git add .pre-commit-config.yaml
git commit -m "chore: update pre-commit hook versions"
```

Run quarterly or whenever a CVE affects a hook dependency.

---

## CI Enforcement

Once a CI pipeline exists, add this step before any build step:

```yaml
- name: Run pre-commit hooks
  run: |
    pip install pre-commit
    pre-commit run --all-files
```

This scan must pass before any other job may proceed.

---

## If a Secret Is Accidentally Committed

**This is a security incident. Act immediately — do not delay to finish other work.**

1. **Rotate the credential.** Revoke and reissue before anything else. Assume it is
   already compromised.

2. **Scrub the history** using BFG Repo Cleaner (preferred over `git filter-branch`):

```bash
# Replace a specific string across all history:
java -jar bfg.jar --replace-text secrets.txt my-repo.git

# Or delete a specific file from all history:
java -jar bfg.jar --delete-files <filename> my-repo.git

# Then clean up refs and force-push:
cd my-repo.git
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push --force-with-lease
```

3. **Force-push** requires human approval (RULES.md §6). Do not proceed unilaterally.

4. **Notify all parties** with a clone — they may have the secret cached locally.

5. **Audit access logs** — determine whether the exposed credential was used.

6. **Document the incident** — add a timestamped entry to the project's incident log or PR.
