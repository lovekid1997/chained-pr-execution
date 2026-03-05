# Chained PR Execution Skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blue)](https://claude.ai/code)

A [Claude Code](https://claude.ai/code) skill that transforms GitHub issues into **chained Pull Requests** with intelligent splitting and linked comments.

## The Problem

Large features are hard to review. A single 1000-line PR is:
- Difficult to understand
- Risky to merge
- Slow to review

## The Solution

Split into **atomic, chained PRs** that tell a story:

```
Issue #123: Add search feature
         │
         ▼
    ┌─────────┐     ┌─────────┐     ┌─────────┐
    │  PR A   │ ──► │  PR B   │ ──► │  PR C   │
    │  Utils  │     │ Feature │     │  Tests  │
    └─────────┘     └─────────┘     └─────────┘
       100 LOC        200 LOC         150 LOC

Each PR: Small, focused, reviewable
Comments: Linked, forming a narrative
```

## Installation

```bash
# Personal (all projects)
git clone https://github.com/lovekid1997/chained-pr-execution.git ~/.claude/skills/chained-pr-execution

# Or project-specific
git clone https://github.com/lovekid1997/chained-pr-execution.git .claude/skills/chained-pr-execution
```

## Usage

```bash
/chain-pr <issue-number>
```

### Example

```bash
/chain-pr 123
```

Claude will:
1. Read issue #123 and parse the HOW section
2. Create an execution plan (split into A/B/C layers)
3. Guide you through implementation
4. Create chained PRs with proper base branches
5. Add linked chain report comments

## How It Works

### 1. Issue with HOW

Your issue should have a HOW section:

```markdown
## Problem
Need to add full-text search to the application.

## HOW
1. Create search utility functions (indexer, query parser)
2. Add SearchQuery and SearchResult types
3. Implement search service
4. Add /search API endpoint
5. Write integration tests
6. Update API docs
```

### 2. Automatic Layer Mapping

| HOW Step | Layer | Why |
|----------|-------|-----|
| Search utilities | **A** | Foundation - others depend on this |
| Types | **A** | Foundation - shared definitions |
| Search service | **B** | Feature - main logic |
| API endpoint | **B** | Feature - uses service |
| Tests | **C** | Integration - tests complete feature |
| Docs | **C** | Integration - documents final API |

### 3. Chained PRs

```
main
 └── PR A: [A] #123 - Search utilities & types
      └── PR B: [B] #123 - Search service & endpoint
           └── PR C: [C] #123 - Tests & documentation
```

**Merge order**: A → B → C (always)

### 4. Linked Comments

Each PR gets a **Chain Report** comment:

```markdown
## 🔗 Chain Report

**Issue**: #123
**Position**: B (middle in chain)
**Depends On**: #455
**Unblocks**: #457

### 📋 Changes in This PR
- Implement search service using utilities from PR A
- Add /search API endpoint

### 🔄 Delta from Previous PR
**PR A established**: Search utilities, SearchQuery types
**This PR adds**: Actual service and endpoint that USE those utilities
**Why separate**: Service is feature code; utilities are foundation

### ➡️ What Next PR Will Add
PR C will add integration tests and API documentation.
Separated because tests should verify the complete feature.

### 📊 Chain Overview
| PR | Status | Description |
|----|--------|-------------|
| #455 | ✅ Merged | Search utilities |
| #456 | 👀 This PR | Search service |
| #457 | 🔄 Open | Tests & docs |
```

**Key**: Comments are NOT independent. They reference each other and form a coherent narrative.

## PR Layers Explained

| Layer | Name | Contains | Size Target |
|-------|------|----------|-------------|
| **A** | Foundation | Utils, types, configs, shared code | 100-200 LOC |
| **B** | Feature | Main implementation, business logic | 200-300 LOC |
| **C** | Integration | Tests, docs, exports, wiring | 100-200 LOC |

### Why This Split?

- **A first**: Others depend on it. Can be reviewed independently.
- **B second**: Uses A. Contains the "meat" of the feature.
- **C last**: Tests the complete feature. Documents the final API.

## Directory Structure

```
chained-pr-execution/
├── SKILL.md                    # Main skill (invoke with /chain-pr)
│
├── rules/
│   ├── issue-parsing.md        # How to parse HOW from issues
│   ├── pr-splitting-rule.md    # Atomic PR splitting rules
│   ├── pr-chain-rule.md        # Chain creation & merge order
│   ├── pr-comment-format.md    # Comment template
│   └── comment-linking-rule.md # How comments reference each other
│
├── templates/
│   ├── pr_description.md       # PR description template
│   └── chain_report_comment.md # Chain report template
│
└── schema/
    ├── pr_chain.json           # Validate PR chain structure
    └── chain_comment.json      # Validate comment structure
```

## Customization

### Add More Layers

For larger features, use sub-layers:

```json
// schema/pr_chain.json
"layer": {
  "enum": ["A", "A1", "A2", "B", "B1", "B2", "C", "C1", "C2"]
}
```

### Change Branch Naming

Edit `rules/pr-chain-rule.md`:

```
feat/<issue>-<layer>[-description]
→ feat/123-a-search-utils
→ feat/123-b-search-service
```

### Modify Comment Format

Edit `templates/chain_report_comment.md` to match your team's style.

## Best Practices

1. **Parse HOW first** - Don't code without a plan
2. **Keep PRs atomic** - One logical change per PR
3. **Update all comments** - When any PR status changes
4. **Merge in order** - Always A → B → C
5. **Rebase the chain** - When main is updated, rebase A, then B, then C

## Requirements

- [Claude Code](https://claude.ai/code) CLI installed
- [GitHub CLI](https://cli.github.com/) (`gh`) authenticated
- Git

## Philosophy

This skill implements a **Stacked PR Workflow** similar to:
- [Graphite](https://graphite.dev/)
- [Gerrit patch chains](https://gerrit-review.googlesource.com/Documentation/)
- [git-branchless](https://github.com/arxanas/git-branchless)

But integrated directly into Claude Code for AI-assisted execution.

## Contributing

1. Fork the repo
2. Create your feature branch
3. Commit changes
4. Push and create a PR

## License

MIT

## Related

- [Agent Skills Standard](https://agentskills.io)
- [Claude Code Documentation](https://docs.anthropic.com/claude-code)
