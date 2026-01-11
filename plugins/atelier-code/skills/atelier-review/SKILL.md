---
name: atelier-review
description: Review code changes as a senior engineer. Use when doing code reviews, checking PR quality, or auditing changes before commit.
user-invocable: true
---

# Code Review

## Step 1: Gather Context

Current branch: !`git branch --show-current`

**Repository status:**
!`git status --porcelain`

**Recent commits (if any):**
!`git log --oneline -5 2>/dev/null || echo "No commits yet"`

**Staged changes:**
!`git diff --cached --name-status`

**Unstaged changes:**
!`git diff --name-status`

**Full diff for review:**
!`git diff HEAD 2>/dev/null || git diff --cached`

## Step 1.2: Read Project Standards

<context>
@agent-clerk

Load project standards for review criteria:
- Read `docs/standards/coding.md` (Stub-Driven TDD, testing patterns)
- Read `docs/standards/architecture.md` (layered architecture)

If standards files don't exist, use default SDD patterns.
</context>

## Step 2: Senior Engineer Review

<architect>
@agent-architect

Analyze all changes with senior engineer perspective.

Focus areas:
**🔴 CRITICAL:** Security vulnerabilities, data loss risks, breaking changes
**🟡 IMPORTANT:** Bugs, performance issues, maintainability concerns
**🟢 MINOR:** Style inconsistencies, naming improvements, optimizations

**Review Standards:**

- Follow `docs/standards/coding.md` (Stub-Driven TDD, testing patterns)
- Follow `docs/standards/architecture.md` (layered architecture)
- Validate boundary testing and anti-patterns per standards
- Ensure proper dependency injection and layer separation

**Architecture Compliance:**
- Check against layered architecture: Router → Service → Repository → Entity
- Ensure proper Entity transformations (fromRequest, toRecord, toResponse)
- Validate stub-driven TDD patterns

Generate specific, actionable feedback with file locations and suggested improvements.
</architect>

## Step 3: Review Report

**Summary:** Brief overview of changes and overall assessment

**Issues by Severity:**

- 🔴 **STANDARDS VIOLATIONS:** `docs/standards/` non-compliance
- 🔴 `file:line` - Issue description → Suggested fix
- 🟡 `file:line` - Issue description → Suggested fix
- 🟢 `file:line` - Issue description → Suggested fix

**👍 Positive Aspects:** Highlight good practices and quality code

**Recommendations:**

- [ ] Required changes before merge
- [ ] Suggested improvements for future
- [ ] Testing recommendations

**Overall Assessment:** [APPROVE/REQUEST CHANGES/NEEDS DISCUSSION]
