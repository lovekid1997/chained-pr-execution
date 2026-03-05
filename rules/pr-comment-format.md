# PR Comment Format

## Chain Report Template

Every PR in a chain MUST have a comment with this structure:

```markdown
## 🔗 Chain Report

**Issue**: #<issue-number>
**Position**: <A|B|C> (<first|middle|last> in chain)
**Depends On**: #<pr-number> or "None (first in chain)"
**Unblocks**: #<pr-number> or "None (last in chain)"

---

### 📋 Changes in This PR

<bullet list of changes>

---

### 🔄 Delta from Previous PR

<what this PR adds ON TOP of the previous PR>
<why these changes couldn't be in the previous PR>

---

### ➡️ What Next PR Will Add

<preview of what the next PR will build on this>
<why those changes are in a separate PR>

---

### ⚠️ Review Notes

- **Risk Level**: <Low|Medium|High>
- **Key Files**: <list critical files to review carefully>
- **Testing**: <how to verify this PR works>

---

### 📊 Chain Overview

| PR | Status | Description |
|----|--------|-------------|
| #A | ✅ Merged / 🔄 Open / 👀 Review | <brief> |
| #B | ✅ Merged / 🔄 Open / 👀 Review | <brief> |
| #C | ✅ Merged / 🔄 Open / 👀 Review | <brief> |
```

## Examples

### PR A Comment (First in Chain)
```markdown
## 🔗 Chain Report

**Issue**: #123
**Position**: A (first in chain)
**Depends On**: None (first in chain)
**Unblocks**: #456

---

### 📋 Changes in This PR

- Add `buildSearchIndex()` utility function
- Add `SearchQuery` and `SearchResult` type definitions
- Add search configuration constants

---

### 🔄 Delta from Previous PR

N/A - This is the foundation PR.

This PR establishes the utilities that will be used by the search service in PR B.

---

### ➡️ What Next PR Will Add

PR B (#456) will:
- Use `buildSearchIndex()` in search service
- Implement /search API endpoint
- Add query parsing logic

These are separated because the service depends on these utilities existing first.

---

### ⚠️ Review Notes

- **Risk Level**: Low
- **Key Files**: `src/utils/search.ts`
- **Testing**: Unit tests included, run `npm test -- search`

---

### 📊 Chain Overview

| PR | Status | Description |
|----|--------|-------------|
| #455 | 👀 This PR | Search utilities |
| #456 | 🔄 Open | Search service |
| #457 | 🔄 Open | Tests & docs |
```

### PR B Comment (Middle of Chain)
```markdown
## 🔗 Chain Report

**Issue**: #123
**Position**: B (middle in chain)
**Depends On**: #455
**Unblocks**: #457

---

### 📋 Changes in This PR

- Add `SearchService` using utilities from PR A
- Implement `/search` API endpoint
- Add query parsing and filtering logic

---

### 🔄 Delta from Previous PR

**Building on PR A (#455)**:
- PR A added the search utilities
- This PR USES those utilities to build the actual service
- Separated because: service logic is feature code, utilities are foundation

---

### ➡️ What Next PR Will Add

PR C (#457) will:
- Add integration tests for search flow
- Update API documentation
- Export search module from index

Separated because: tests should verify the complete feature, not partial implementation.

---

### ⚠️ Review Notes

- **Risk Level**: Medium
- **Key Files**: `src/services/search.ts`, `src/routes/search.ts`
- **Testing**: Requires PR A merged first, then `npm test -- search`

---

### 📊 Chain Overview

| PR | Status | Description |
|----|--------|-------------|
| #455 | ✅ Merged | Search utilities |
| #456 | 👀 This PR | Search service |
| #457 | 🔄 Open | Tests & docs |
```

## Comment Update Rules

1. **When PR status changes**: Update the Chain Overview table in ALL related PRs
2. **When PR is merged**: Add merge commit SHA to the report
3. **When rebased**: Note the rebase in comments

## Quick Copy Template

```bash
gh pr comment <PR_NUMBER> --body "$(cat <<'EOF'
## 🔗 Chain Report

**Issue**: #
**Position**:
**Depends On**:
**Unblocks**:

---

### 📋 Changes in This PR



---

### 🔄 Delta from Previous PR



---

### ➡️ What Next PR Will Add



---

### ⚠️ Review Notes

- **Risk Level**:
- **Key Files**:
- **Testing**:

---

### 📊 Chain Overview

| PR | Status | Description |
|----|--------|-------------|
|  |  |  |
EOF
)"
```
