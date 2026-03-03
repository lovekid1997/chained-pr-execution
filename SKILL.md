---
name: chain-pr
description: Execute GitHub issue with chained PR workflow - read issue HOW, implement, split into atomic PRs with dependency chain A <- B <- C
disable-model-invocation: true
allowed-tools: Read, Grep, Glob, Bash(gh *), Bash(git *), Write, Edit
argument-hint: [issue-number]
---

# Chained PR Execution Workflow

Execute issue **#$ARGUMENTS** following the company's chained PR workflow.

## Workflow Overview

```
Issue (with HOW) → Implement → Split PRs → Chain (A←B←C) → Report Comments
```

## Step 1: Parse Issue

```bash
gh issue view $ARGUMENTS --json title,body,labels
```

Extract from issue:
- **Goal**: What needs to be achieved
- **HOW**: Step-by-step implementation guide
- **Scope**: Files/modules affected
- **Dependencies**: External dependencies

## Step 2: Create Execution Plan

Before coding, create a PR split plan:

| PR | Layer | Description | Depends On | Base Branch |
|----|-------|-------------|------------|-------------|
| A  | Foundation | Core changes, shared utils | - | main |
| B  | Feature | Main implementation | A | pr-A-branch |
| C  | Integration | Tests, docs, final wiring | B | pr-B-branch |

See [pr-splitting-rule.md](rules/pr-splitting-rule.md) for detailed rules.

## Step 3: Implement Following HOW

Follow the HOW in issue exactly. Track changes by layer:
- Foundation changes → PR A
- Feature changes → PR B
- Integration changes → PR C

## Step 4: Create Chained PRs

For each PR, follow [pr-chain-rule.md](rules/pr-chain-rule.md):

```bash
# PR A (foundation)
git checkout -b feat/$ARGUMENTS-a main
# ... commit changes
gh pr create --base main --title "[A] Issue #$ARGUMENTS: Foundation"

# PR B (depends on A)
git checkout -b feat/$ARGUMENTS-b feat/$ARGUMENTS-a
# ... commit changes
gh pr create --base feat/$ARGUMENTS-a --title "[B] Issue #$ARGUMENTS: Feature"

# PR C (depends on B)
git checkout -b feat/$ARGUMENTS-c feat/$ARGUMENTS-b
# ... commit changes
gh pr create --base feat/$ARGUMENTS-b --title "[C] Issue #$ARGUMENTS: Integration"
```

## Step 5: Add Chain Report Comments

Each PR MUST have a comment following [pr-comment-format.md](rules/pr-comment-format.md).

Comments are NOT independent - they reference each other and form a coherent narrative.

See [comment-linking-rule.md](rules/comment-linking-rule.md) for linking rules.

## Validation Checklist

- [ ] Each PR is atomic (single logical change)
- [ ] PR chain is correct (A <- B <- C)
- [ ] Each PR has chain report comment
- [ ] Comments reference related PRs
- [ ] All tests pass on each PR

## Quick Commands

```bash
# View PR chain
gh pr list --search "Issue #$ARGUMENTS"

# Check PR status
gh pr checks <pr-number>

# Add comment
gh pr comment <pr-number> --body "..."
```
