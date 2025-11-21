# Security Guide - Protecting Credentials in coursera-test

This guide provides detailed instructions for handling sensitive information in this repository, especially when credentials have been accidentally committed to git.

## Table of Contents

1. [Prevention - Before You Commit](#prevention---before-you-commit)
2. [Credentials Already Committed?](#credentials-already-committed)
3. [Removing Sensitive Data from Git History](#removing-sensitive-data-from-git-history)
4. [Alternative Methods](#alternative-methods)
5. [After Cleanup](#after-cleanup)
6. [Best Practices](#best-practices)

---

## Prevention - Before You Commit

### Always Use .env Files

**DO**:
```bash
# Copy the example file
cp .env.example .env

# Edit .env with your real credentials
# .env is in .gitignore and won't be committed
```

**DON'T**:
```bash
# Never commit files with real credentials
git add config/credentials.json  # ❌ NO!
git add .env                      # ❌ NO!
```

### Check Before Committing

Before every commit, verify you're not adding sensitive files:

```bash
# Review what you're about to commit
git status
git diff --staged

# Search for credential patterns in staged files
git diff --staged | grep -E "(password|api_key|secret|token)" -i
```

### Use Git Hooks (Optional)

Create a pre-commit hook to scan for secrets:

```bash
# Create .git/hooks/pre-commit
#!/bin/bash

# Scan for common credential patterns
if git diff --cached | grep -qE "(password|passwd|api_key|secret_key|private_key)[\s]*[=:]"; then
    echo "❌ Error: Possible credentials detected in staged changes!"
    echo "Please review your changes and remove any sensitive data."
    exit 1
fi
```

```bash
# Make it executable
chmod +x .git/hooks/pre-commit
```

---

## Credentials Already Committed?

### CRITICAL: Act Immediately

If you've already committed and pushed credentials:

1. **🚨 FIRST: Rotate the compromised credentials immediately**
   - Change passwords
   - Regenerate API keys
   - Revoke access tokens
   - Update service credentials

2. **Then: Clean the git history** (see below)

### Why Rotation is Critical

Once credentials are pushed to a public repository:
- They are publicly visible
- They may be cached by GitHub/services
- Bots may have already scraped them
- Simply removing the file doesn't remove from history

**Treat all exposed credentials as compromised.**

---

## Removing Sensitive Data from Git History

### Prerequisites

Before cleaning git history:

1. **Notify all team members** - they'll need to re-clone
2. **Back up the repository** - make a copy somewhere safe
3. **Coordinate timing** - no one should push during cleanup

### Method 1: Using git-filter-repo (Recommended)

`git-filter-repo` is the modern, safe way to rewrite history.

#### Install git-filter-repo

```bash
# macOS
brew install git-filter-repo

# Ubuntu/Debian
apt-get install git-filter-repo

# Or via pip
pip3 install git-filter-repo
```

#### Remove a Specific File

```bash
# Remove a file from all commits
git filter-repo --path path/to/sensitive-file.env --invert-paths

# Remove multiple files
git filter-repo --path config/credentials.json \
                --path .env \
                --path secrets.yaml \
                --invert-paths

# Remove all .env files recursively
git filter-repo --path-glob '**/.env' --invert-paths
```

#### Replace Content in Files

If you need to keep the file but remove sensitive content:

```bash
# Create a replacements file
echo "old_password==>REDACTED" >> replacements.txt
echo "api_key_12345==>REDACTED" >> replacements.txt

# Replace the content
git filter-repo --replace-text replacements.txt
```

### Method 2: Using BFG Repo-Cleaner

BFG is faster for simple cases but less flexible.

#### Install BFG

```bash
# Download BFG
wget https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar

# Or via Homebrew
brew install bfg
```

#### Remove Files

```bash
# Remove a specific file
java -jar bfg.jar --delete-files credentials.json

# Or if installed via Homebrew
bfg --delete-files credentials.json

# Remove files matching a pattern
bfg --delete-files '*.env'

# Remove files in a specific folder
bfg --delete-folders config/secrets
```

#### Replace Text

```bash
# Create passwords.txt with one password per line
echo "my_secret_password" >> passwords.txt
echo "abc123apikey" >> passwords.txt

# Replace all occurrences
bfg --replace-text passwords.txt
```

### Method 3: Manual with git filter-branch (Legacy)

**Note**: git filter-branch is deprecated. Use git-filter-repo instead.

If you must use filter-branch:

```bash
# Remove a file from all commits
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch path/to/file" \
  --prune-empty --tag-name-filter cat -- --all

# Remove all .env files
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch -r '*.env'" \
  --prune-empty --tag-name-filter cat -- --all
```

---

## Alternative Methods

### GitHub Secret Scanning

GitHub automatically scans public repositories for known secret formats. If detected, they'll notify you.

To check manually:
1. Go to your repository on GitHub
2. Settings → Security → Secret scanning alerts

### Using GitHub Support

For public repositories with leaked secrets:

1. Contact GitHub Support immediately
2. They can help remove cached versions
3. They may take down the repository temporarily

### Make Repository Private (Temporary)

If the repository is currently public:

```bash
# On GitHub:
# Settings → General → Danger Zone → Change visibility → Make private
```

This stops new access but doesn't remove from caches or scrapers.

---

## After Cleanup

### Force Push to Remote

After cleaning history, update the remote repository:

```bash
# Ensure you have the latest changes
git fetch origin

# Force push all branches
git push origin --force --all

# Force push all tags
git push origin --force --tags
```

### Clean Local Clones

Everyone must re-clone or reset their local repositories:

```bash
# Option 1: Re-clone (safest)
cd ..
rm -rf coursera-test
git clone <repository-url>

# Option 2: Reset local repository
git fetch origin
git reset --hard origin/main  # or your branch name
git clean -fdx
git reflog expire --expire=now --all
git gc --prune=now --aggressive
```

### Verify Cleanup

Check that sensitive data is gone:

```bash
# Search for credential patterns
git log --all --full-history --source -- '*credentials*'
git log --all -S "password" --source --all

# Check using git-filter-repo
git filter-repo --analyze
```

### Update Protected Branches

If you have protected branches:

1. Temporarily disable branch protection
2. Force push
3. Re-enable branch protection

---

## Best Practices

### 1. Use Environment Variables

Always store sensitive configuration in environment variables:

```javascript
// Good ✅
const apiKey = process.env.API_KEY;

// Bad ❌
const apiKey = "sk-abc123xyz789";
```

### 2. Use Secret Management Services

For production applications, consider:

- **AWS Secrets Manager**: For AWS infrastructure
- **HashiCorp Vault**: For multi-cloud and on-premise
- **Azure Key Vault**: For Azure infrastructure
- **Google Secret Manager**: For GCP infrastructure
- **GitHub Secrets**: For CI/CD pipelines

### 3. Different Credentials Per Environment

Never reuse credentials across environments:

```
Development: dev_api_key_123
Staging: stg_api_key_456
Production: prd_api_key_789
```

### 4. Regular Security Audits

Schedule regular checks:

```bash
# Monthly: Scan for exposed secrets
git log --all -p | grep -E "(password|api_key|secret)" -i

# Quarterly: Review access permissions
# - Who has repository access?
# - Are old team members removed?
# - Are service accounts still needed?
```

### 5. Principle of Least Privilege

- Grant minimum necessary permissions
- Use read-only credentials where possible
- Implement credential rotation policies
- Set expiration dates on tokens

### 6. Documentation

Document your credential management:

- Where credentials are stored
- How to access them
- Rotation procedures
- Emergency contacts

### 7. Training

Ensure all team members understand:

- Why credential security matters
- How to use .env files
- What to do if credentials are exposed
- Company security policies

---

## Quick Reference: Emergency Checklist

If credentials are exposed in public repository:

- [ ] **Immediately** rotate/change ALL exposed credentials
- [ ] Make repository private (temporary)
- [ ] Notify security team/stakeholders
- [ ] Back up the repository
- [ ] Choose cleanup method (git-filter-repo recommended)
- [ ] Clean git history
- [ ] Force push to remote
- [ ] Notify team members to re-clone
- [ ] Verify cleanup was successful
- [ ] Document the incident
- [ ] Review and improve security practices
- [ ] Consider if breach reporting is required

---

## Additional Resources

- [GitHub: Removing sensitive data](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
- [git-filter-repo documentation](https://github.com/newren/git-filter-repo)
- [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)
- [OWASP: Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- [OWASP: Secrets Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)

---

## Questions?

If you need help with security issues:

1. Check this guide first
2. Consult with security team
3. Review GitHub documentation
4. When in doubt, treat as compromised

**Remember**: It's better to be overly cautious with security than to risk a breach.

---

*Last updated: 2025-11-21*
