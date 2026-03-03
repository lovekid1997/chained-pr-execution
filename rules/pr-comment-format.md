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

- Add `validateToken()` utility function
- Add `TokenPayload` type definition
- Add JWT configuration constants

---

### 🔄 Delta from Previous PR

N/A - This is the foundation PR.

This PR establishes the utilities that will be used by the auth middleware in PR B.

---

### ➡️ What Next PR Will Add

PR B (#456) will:
- Use `validateToken()` in auth middleware
- Implement login/logout routes
- Add session management

These are separated because the middleware depends on these utilities existing first.

---

### ⚠️ Review Notes

- **Risk Level**: Low
- **Key Files**: `src/utils/jwt.ts`
- **Testing**: Unit tests included, run `npm test -- jwt`

---

### 📊 Chain Overview

| PR | Status | Description |
|----|--------|-------------|
| #455 | 👀 This PR | JWT utilities |
| #456 | 🔄 Open | Auth middleware |
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

- Add `authMiddleware` using utilities from PR A
- Implement `/login` and `/logout` routes
- Add session storage logic

---

### 🔄 Delta from Previous PR

**Building on PR A (#455)**:
- PR A added the JWT utilities
- This PR USES those utilities to build the actual middleware
- Separated because: middleware logic is feature code, utilities are foundation

---

### ➡️ What Next PR Will Add

PR C (#457) will:
- Add integration tests for auth flow
- Update API documentation
- Export auth module from index

Separated because: tests should verify the complete feature, not partial implementation.

---

### ⚠️ Review Notes

- **Risk Level**: Medium
- **Key Files**: `src/middleware/auth.ts`, `src/routes/auth.ts`
- **Testing**: Requires PR A merged first, then `npm test -- auth`

---

### 📊 Chain Overview

| PR | Status | Description |
|----|--------|-------------|
| #455 | ✅ Merged | JWT utilities |
| #456 | 👀 This PR | Auth middleware |
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
