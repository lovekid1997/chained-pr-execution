# Comment Linking Rules

## Core Principle

PR comments in a chain are NOT independent documents.
They form a **coherent narrative** that tells the complete story of the implementation.

## Linking Requirements

### 1. Explicit References

Every comment MUST reference:
- The issue it solves: `#<issue-number>`
- Previous PR in chain: `Depends On: #<pr-number>`
- Next PR in chain: `Unblocks: #<pr-number>`

### 2. Narrative Continuity

| Section | Must Reference |
|---------|---------------|
| Delta from Previous | What PR A/B established |
| What Next PR Will Add | What PR B/C will build |
| Chain Overview | All PRs with current status |

### 3. Cross-Reference Format

When referencing another PR's content:
```markdown
As established in PR A (#455):
> "Added buildSearchIndex() utility that handles indexing"

This PR extends that by...
```

## Linking Patterns

### Pattern 1: Foundation Reference
```markdown
### 🔄 Delta from Previous PR

**Foundation from #455**:
- Types: `SearchQuery`, `SearchResult`
- Utils: `buildSearchIndex()`, `parseQuery()`

**This PR adds**:
- Service that USES these types and utils
- Route handlers that CALL the service
```

### Pattern 2: Preview Link
```markdown
### ➡️ What Next PR Will Add

PR C (#457) will complete the feature by:
- Testing the search flow we built here
- Documenting the API endpoints
- Exporting for external use

**Why separate?**
Tests require the complete implementation to exist.
Docs should describe the final API, not intermediate states.
```

### Pattern 3: Status Sync
When ANY PR in chain changes status, update ALL chain comments:

```markdown
### 📊 Chain Overview

| PR | Status | Description |
|----|--------|-------------|
| #455 | ✅ Merged (abc123) | Search utilities |
| #456 | 👀 In Review | Search service |
| #457 | ⏸️ Blocked by #456 | Tests & docs |
```

## Automated Linking Script

```bash
#!/bin/bash
# Update chain overview in all PRs

ISSUE_NUM=$1
PR_A=$2
PR_B=$3
PR_C=$4

# Get current statuses
STATUS_A=$(gh pr view $PR_A --json state -q '.state')
STATUS_B=$(gh pr view $PR_B --json state -q '.state')
STATUS_C=$(gh pr view $PR_C --json state -q '.state')

# Generate overview table
OVERVIEW="| PR | Status | Description |
|----|--------|-------------|
| #$PR_A | $STATUS_A | Foundation |
| #$PR_B | $STATUS_B | Feature |
| #$PR_C | $STATUS_C | Integration |"

echo "$OVERVIEW"
```

## Validation Checklist

For each PR comment, verify:

- [ ] Issue number is referenced
- [ ] Depends On PR is linked (or "None" if first)
- [ ] Unblocks PR is linked (or "None" if last)
- [ ] Delta section references previous PR's changes
- [ ] Next section previews upcoming PR
- [ ] Chain Overview shows all PRs with correct status
- [ ] Any quotes from other PRs are accurate

## Anti-Patterns

### ❌ Isolated Comment
```markdown
## Changes
- Added search service
```
Missing: chain context, dependencies, narrative

### ❌ Broken Links
```markdown
Depends On: #455  <!-- PR was renumbered to #460 -->
```
Always verify PR numbers are current.

### ❌ Stale Overview
```markdown
| #455 | 🔄 Open |  <!-- Actually merged 2 days ago -->
```
Update overview when any PR status changes.

## Update Triggers

Update ALL chain comments when:
1. Any PR is merged
2. Any PR is rebased
3. Any PR scope changes
4. New PR is added to chain
5. PR is removed from chain
