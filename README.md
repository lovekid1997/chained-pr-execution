# Chained PR Execution Skill

A [Claude Code](https://claude.ai/code) skill for executing GitHub issues through chained Pull Requests with linked comments.

## Features

- Parse GitHub issues with HOW implementation guides
- Split implementation into atomic PRs (Foundation → Feature → Integration)
- Create stacked PR chains (A ← B ← C)
- Generate linked chain report comments
- Validate PR structure with JSON schemas

## Installation

### Option 1: Clone to personal skills
```bash
git clone https://github.com/YOUR_ORG/chained-pr-execution.git ~/.claude/skills/chained-pr-execution
```

### Option 2: Clone to project
```bash
git clone https://github.com/YOUR_ORG/chained-pr-execution.git .claude/skills/chained-pr-execution
```

## Usage

```bash
/chain-pr <issue-number>
```

### Example
```bash
/chain-pr 123
```

## Workflow

```
┌─────────────────┐
│  GitHub Issue   │
│   (with HOW)    │
└────────┬────────┘
         ▼
┌─────────────────┐
│  Parse & Plan   │
│   A → B → C     │
└────────┬────────┘
         ▼
┌─────────────────┐
│   Implement     │
│   By Layers     │
└────────┬────────┘
         ▼
┌─────────────────┐
│  Create Chain   │
│   A ← B ← C     │
└────────┬────────┘
         ▼
┌─────────────────┐
│ Linked Comments │
└─────────────────┘
```

## PR Layers

| Layer | Purpose | Examples |
|-------|---------|----------|
| **A** | Foundation | Utils, types, configs |
| **B** | Feature | Main implementation, routes |
| **C** | Integration | Tests, docs, exports |

## Chain Structure

```
main
 └── PR A (foundation)
      └── PR B (feature)
           └── PR C (integration)
```

PRs are merged in order: A → B → C

## Comment Linking

Each PR includes a **Chain Report** comment that:
- References the parent issue
- Links to dependent PRs (Depends On / Unblocks)
- Explains delta from previous PR
- Previews what next PR adds
- Shows chain overview with all PR statuses

### Example Chain Report

```markdown
## 🔗 Chain Report

**Issue**: #123
**Position**: B (middle in chain)
**Depends On**: #455
**Unblocks**: #457

### 📋 Changes in This PR
- Implement auth middleware
- Add login/logout routes

### 🔄 Delta from Previous PR
Building on PR A (#455) which added JWT utilities...

### ➡️ What Next PR Will Add
PR C (#457) will add tests and documentation...

### 📊 Chain Overview
| PR | Status | Description |
|----|--------|-------------|
| #455 | ✅ Merged | JWT utilities |
| #456 | 👀 This PR | Auth middleware |
| #457 | 🔄 Open | Tests & docs |
```

## Directory Structure

```
chained-pr-execution/
├── SKILL.md                    # Main skill entry
├── README.md                   # This file
├── package.json                # Package metadata
├── LICENSE                     # MIT License
│
├── rules/
│   ├── issue-parsing.md        # Parse HOW from issues
│   ├── pr-splitting-rule.md    # Atomic PR rules
│   ├── pr-chain-rule.md        # Chain creation rules
│   ├── pr-comment-format.md    # Comment template
│   └── comment-linking-rule.md # Comment linking rules
│
├── templates/
│   ├── pr_description.md       # PR description template
│   └── chain_report_comment.md # Comment template
│
└── schema/
    ├── pr_chain.json           # PR chain validation
    └── chain_comment.json      # Comment validation
```

## Customization

### Modify PR Layers
Edit `schema/pr_chain.json` to add layers (A1, A2, B1, B2, etc.)

### Change Comment Format
Edit `rules/pr-comment-format.md` and `templates/chain_report_comment.md`

### Adjust Branch Naming
Edit `rules/pr-chain-rule.md` branch naming section

## Requirements

- [Claude Code](https://claude.ai/code) CLI
- GitHub CLI (`gh`) installed and authenticated
- Git

## Related

- [Agent Skills Standard](https://agentskills.io)
- [Claude Code Documentation](https://docs.anthropic.com/claude-code)

## License

MIT
