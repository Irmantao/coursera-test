# CLAUDE.md - AI Assistant Guide for coursera-test

This document provides comprehensive guidance for AI assistants (like Claude) working with the `coursera-test` repository. It covers codebase structure, development workflows, and key conventions to follow.

## Table of Contents

1. [Repository Overview](#repository-overview)
2. [Current Structure](#current-structure)
3. [Development Workflow](#development-workflow)
4. [Git Conventions](#git-conventions)
5. [Security Best Practices](#security-best-practices)
6. [File Organization](#file-organization)
7. [Code Style Guidelines](#code-style-guidelines)
8. [Testing Strategy](#testing-strategy)
9. [Documentation Standards](#documentation-standards)
10. [AI Assistant Best Practices](#ai-assistant-best-practices)
11. [Common Tasks](#common-tasks)

---

## Repository Overview

**Repository**: `Irmantao/coursera-test`
**Purpose**: Test repository for development and experimentation
**Current State**: Minimal - contains only README.md and initial setup

### Key Information

- **Remote**: http://127.0.0.1:37672/git/Irmantao/coursera-test
- **Main Branch**: Not yet established (currently working on feature branches)
- **License**: Not specified
- **Language**: Not yet determined
- **Framework**: Not yet determined

---

## Current Structure

```
coursera-test/
├── .git/                 # Git repository metadata
├── .gitignore           # Git ignore rules for sensitive files
├── .env.example         # Environment variables template
├── README.md            # Repository description
├── CLAUDE.md            # This file - AI assistant guide
└── SECURITY_GUIDE.md    # Security procedures and credential removal guide
```

### Directory Purpose

- **Root Directory**: Contains project configuration files and main documentation
- **.git/**: Version control system metadata (do not modify directly)

---

## Development Workflow

### Branch Strategy

This repository uses a feature branch workflow with specific naming conventions:

1. **Feature Branches**: All development work should be done on feature branches
2. **Branch Naming**: `claude/<session-id>` format
3. **Protection**: Only branches starting with `claude/` and ending with the correct session ID can be pushed

### Development Process

1. **Start Work**: Ensure you're on the correct feature branch
2. **Make Changes**: Implement features or fixes with clear, focused commits
3. **Test**: Verify changes work as expected
4. **Commit**: Create descriptive commit messages
5. **Push**: Push to the feature branch using `git push -u origin <branch-name>`

### Commit Message Format

Follow these conventions for commit messages:

```
<type>: <short description>

<optional longer description>

<optional footer>
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples**:
```
feat: add user authentication module

Implements JWT-based authentication with refresh tokens.
Includes middleware for protected routes.

Closes #123
```

```
fix: resolve null pointer exception in data processor

Added null checks before accessing nested properties.
```

---

## Git Conventions

### Push Operations

- **Always use**: `git push -u origin <branch-name>`
- **Branch naming**: Must start with `claude/` and end with session ID
- **Retry logic**: If push fails due to network errors, retry up to 4 times with exponential backoff (2s, 4s, 8s, 16s)
- **Never**: Push to branches without proper naming convention (will fail with 403)

### Fetch/Pull Operations

- **Prefer**: `git fetch origin <branch-name>` for specific branches
- **Pull**: `git pull origin <branch-name>` when needed
- **Retry**: Up to 4 times with exponential backoff if network failures occur

### Branch Management

- **Check branch**: Always verify current branch before making changes
- **Create branches**: Use `git checkout -b claude/<session-id>` for new branches
- **Never**: Force push without explicit permission
- **Never**: Modify main/master branch directly

---

## Security Best Practices

### Credential Management

**CRITICAL**: Never commit sensitive information to the repository. This includes:

- Passwords and API keys
- Database connection strings with credentials
- Private keys and certificates
- Access tokens and secrets
- Environment-specific configuration with sensitive data

### Using Environment Variables

1. **Store credentials in .env files**:
   - Copy `.env.example` to `.env`
   - Fill in your actual credentials in `.env`
   - `.env` is in `.gitignore` and will never be committed

2. **Example usage**:
   ```javascript
   // Node.js example
   require('dotenv').config();
   const apiKey = process.env.API_KEY;
   ```

   ```python
   # Python example
   import os
   from dotenv import load_dotenv
   load_dotenv()
   api_key = os.getenv('API_KEY')
   ```

3. **Update .env.example**:
   - When adding new environment variables, update `.env.example`
   - Use placeholder values, never real credentials
   - Document what each variable is for

### Files to Never Commit

The `.gitignore` file protects against committing:

- `.env`, `.env.*` - Environment variables
- `**/credentials.*`, `**/secrets.*` - Credential files
- `**/*.pem`, `**/*.key` - Private keys
- `**/password*`, `**/token*` - Password/token files
- Cloud provider credentials (`.aws/credentials`, etc.)

### What to Do If Credentials Were Committed

If credentials were accidentally committed to the repository:

1. **IMMEDIATELY rotate/change the exposed credentials**
   - Change passwords
   - Regenerate API keys
   - Revoke access tokens

2. **Remove from git history** (see SECURITY_GUIDE.md for detailed steps):
   ```bash
   # Option 1: Using git filter-repo (recommended)
   git filter-repo --path-match path/to/sensitive/file --invert-paths

   # Option 2: Using BFG Repo-Cleaner
   bfg --delete-files credentials.json
   bfg --replace-text passwords.txt
   ```

3. **Force push to update remote**:
   ```bash
   git push origin --force --all
   ```

4. **Notify team members** to re-clone the repository

### AI Assistant Security Responsibilities

When working with this repository, AI assistants MUST:

1. **Never generate or commit real credentials**
2. **Always use .env files for sensitive configuration**
3. **Check for credential patterns before committing**
4. **Warn users if sensitive files are about to be committed**
5. **Refuse to commit files like**:
   - `.env` (unless specifically `.env.example`)
   - `credentials.json`, `secrets.yaml`, etc.
   - Any file containing passwords or API keys

### Security Scanning

Before committing, check for common credential patterns:

```bash
# Search for potential secrets
grep -r -E "(password|passwd|pwd|api_key|secret|token|auth)[\s]*[=:]" --exclude-dir=.git --exclude="*.md" .

# Use git-secrets (if installed)
git secrets --scan
```

### Code Security

Avoid common vulnerabilities:

1. **SQL Injection**: Use parameterized queries
2. **XSS**: Sanitize user input, escape output
3. **CSRF**: Use CSRF tokens for state-changing operations
4. **Command Injection**: Never pass unsanitized input to system commands
5. **Path Traversal**: Validate file paths
6. **Insecure Dependencies**: Keep dependencies updated

### Encryption and Hashing

- **Passwords**: Always hash (bcrypt, argon2, etc.), never store plaintext
- **Sensitive data**: Encrypt at rest and in transit
- **API communication**: Use HTTPS/TLS
- **Tokens**: Use secure, random generation (crypto libraries)

---

## File Organization

As the repository grows, follow these conventions:

### Suggested Structure

```
coursera-test/
├── src/                  # Source code
│   ├── components/       # Reusable components
│   ├── services/         # Business logic services
│   ├── utils/           # Utility functions
│   └── index.js         # Entry point
├── tests/               # Test files
│   ├── unit/           # Unit tests
│   └── integration/    # Integration tests
├── docs/               # Additional documentation
├── config/             # Configuration files
├── scripts/            # Build and deployment scripts
├── .gitignore          # Git ignore rules
├── package.json        # Node.js dependencies (if applicable)
├── README.md           # Project overview
└── CLAUDE.md           # This file
```

### Naming Conventions

- **Files**: Use kebab-case for file names (e.g., `user-service.js`)
- **Directories**: Use kebab-case for directory names (e.g., `api-handlers`)
- **Classes**: Use PascalCase for class names (e.g., `UserController`)
- **Functions**: Use camelCase for function names (e.g., `getUserById`)
- **Constants**: Use UPPER_SNAKE_CASE for constants (e.g., `MAX_RETRY_COUNT`)

---

## Code Style Guidelines

### General Principles

1. **Clarity**: Write code that is easy to read and understand
2. **Consistency**: Follow established patterns in the codebase
3. **Simplicity**: Prefer simple solutions over complex ones
4. **DRY**: Don't Repeat Yourself - extract common functionality
5. **YAGNI**: You Aren't Gonna Need It - don't add unnecessary features

### Language-Specific Guidelines

These will be added as the project's primary language is determined.

### Comments and Documentation

- **Code Comments**: Explain WHY, not WHAT
- **Function Documentation**: Document parameters, return values, and side effects
- **Complex Logic**: Add explanatory comments for non-obvious code
- **TODOs**: Use `// TODO: description` format for future work
- **Avoid**: Commenting out code - delete it instead (version control preserves history)

---

## Testing Strategy

### Testing Principles

1. **Write Tests**: All new features should include tests
2. **Test Coverage**: Aim for meaningful coverage, not just high percentages
3. **Test Types**:
   - Unit tests for individual functions/components
   - Integration tests for component interactions
   - End-to-end tests for critical user flows

### Test Organization

- **Location**: Mirror source structure in `tests/` directory
- **Naming**: Use `.test.js` or `.spec.js` suffix
- **Isolation**: Tests should be independent and not rely on execution order

### Running Tests

Commands will be added as the testing framework is established.

---

## Documentation Standards

### README.md

- Keep updated with project overview and setup instructions
- Include prerequisites, installation steps, and usage examples
- Add badges for build status, coverage, etc. when applicable

### CLAUDE.md (This File)

- Update when development patterns change
- Add new sections as the project grows
- Keep examples current and relevant

### Code Documentation

- Use JSDoc, TypeDoc, or similar based on language choice
- Document public APIs thoroughly
- Include examples for complex functionality

### Inline Documentation

- Explain complex algorithms or business logic
- Document assumptions and constraints
- Note any known limitations or edge cases

---

## AI Assistant Best Practices

### Before Making Changes

1. **Understand Context**: Read relevant files and documentation
2. **Check Current Branch**: Verify you're on the correct feature branch
3. **Review Recent Commits**: Understand recent changes
4. **Ask Questions**: If requirements are unclear, ask for clarification

### While Working

1. **Use Todo Lists**: Track tasks with TodoWrite tool for complex work
2. **Make Focused Changes**: Keep commits focused on single concerns
3. **Test Changes**: Verify modifications work as expected
4. **Security First**: Watch for vulnerabilities (XSS, SQL injection, etc.)
5. **Parallel Operations**: Run independent operations concurrently

### Communication

1. **Be Concise**: Provide clear, brief explanations
2. **No Emoji**: Avoid emojis unless explicitly requested
3. **Technical Accuracy**: Prioritize correctness over validation
4. **Objective**: Focus on facts and problem-solving

### Tool Usage

1. **Prefer Specialized Tools**: Use Read/Edit/Write over bash for files
2. **Parallel Calls**: Make independent tool calls simultaneously
3. **Task Agent**: Use Task tool for complex searches/analysis
4. **Avoid Bash**: Don't use bash echo/commands for user communication

---

## Common Tasks

### Setting Up Local Development

```bash
# Clone the repository (if not already cloned)
git clone <repository-url>
cd coursera-test

# Create and checkout feature branch
git checkout -b claude/<session-id>

# Install dependencies (when applicable)
# npm install  # Node.js example
# pip install -r requirements.txt  # Python example
```

### Making Changes

```bash
# Check current status
git status

# Stage changes
git add <files>

# Commit with descriptive message
git commit -m "feat: add new feature"

# Push to remote
git push -u origin claude/<session-id>
```

### Updating Documentation

1. Read the existing documentation
2. Make necessary changes
3. Ensure consistency with existing style
4. Commit with `docs:` prefix

### Adding New Features

1. Create todo list for planning
2. Implement feature in focused commits
3. Add tests for new functionality
4. Update documentation
5. Push changes to feature branch

### Investigating Issues

1. Read relevant source files
2. Check recent changes with `git log`
3. Search codebase for related code
4. Use Task tool for complex exploration
5. Document findings

### Code Review Checklist

- [ ] Code follows style guidelines
- [ ] Tests are included and passing
- [ ] Documentation is updated
- [ ] No security vulnerabilities introduced
- [ ] Commit messages are descriptive
- [ ] Changes are focused and coherent

---

## Repository Evolution

As this repository grows, this document should be updated to reflect:

- Chosen programming language and framework
- Build and deployment processes
- CI/CD pipeline configuration
- Environment setup requirements
- API documentation standards
- Performance considerations
- Security guidelines
- Contribution workflow
- Code review process

---

## Questions or Issues?

If you encounter issues or have questions about conventions:

1. Check existing code for patterns
2. Review git history for context
3. Ask the user for clarification
4. Document decisions in this file

---

## Version History

- **2025-11-21**: Security update
  - Added comprehensive security best practices section
  - Created .gitignore to protect sensitive files
  - Added .env.example template for credentials
  - Added AI assistant security responsibilities

- **2025-11-21**: Initial version created
  - Established basic structure
  - Defined git conventions
  - Set up AI assistant guidelines

---

*This document should be updated regularly to reflect the current state of the repository and development practices.*
