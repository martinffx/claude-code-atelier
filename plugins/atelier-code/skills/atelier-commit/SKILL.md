---
name: atelier-commit
description: Create well-crafted conventional commits. Use when staging changes, creating git commits, or committing code.
user-invocable: true
allowed-tools: Bash(git:*)
---

# Smart Git Commit

<analyze_changes>
## Step 1: Analyze Repository State

Current repository state:
- Git status: !`git status --porcelain`
- Staged changes: !`git diff --cached --name-status`
- Unstaged changes: !`git diff --name-status`
- Current branch: !`git branch --show-current`

Analyze the changes and determine the appropriate commit type and scope.
</analyze_changes>

<generate_message>
## Step 2: Generate Commit Message

Create conventional commit message based on code changes.

**Conventional Commit Rules:**
- **feat(scope)**: New feature or component
- **fix(scope)**: Bug fixes and corrections
- **test(scope)**: Test additions or modifications
- **docs(scope)**: Documentation changes
- **style(scope)**: Formatting, semicolons, etc (no code change)
- **refactor(scope)**: Code restructuring without behavior change
- **perf(scope)**: Performance improvements
- **chore(scope)**: Build process, auxiliary tools, dependencies

**Format:** `type(scope): brief description`
- Keep subject under 50 characters
- Use imperative mood ("Add" not "Added")
- Lowercase subject line
- No period at end of subject
- Include feature context from specs if applicable

**Custom message handling:**
- If $ARGUMENTS provided: Use as commit message
- If no $ARGUMENTS: Generate conventional commit message

Show suggested message: "📝 **Suggested:** `[generated message]`"
</generate_message>

<execute_commit>
## Step 3: Execute Commit

If the suggestion looks good, execute:
- Stage all changes: `git add -A`
- Create commit: `git commit -m "[message]"`
- Confirm: "✅ **Committed!** Ready for `git push`"
</execute_commit>

Focus on creating commits that clearly explain what changed and why.
