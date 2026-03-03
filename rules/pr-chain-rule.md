# PR Chain Rules

## Chain Structure

```
main
 └── PR A (foundation)
      └── PR B (feature)
           └── PR C (integration)
```

## Branch Naming Convention

```
feat/<issue-number>-<layer>[-description]

Examples:
- feat/123-a-add-utils
- feat/123-b-implement-auth
- feat/123-c-add-tests
```

## Base Branch Rules

| PR | Base Branch | Target After Merge |
|----|-------------|-------------------|
| A | `main` | Merge to main first |
| B | `feat/<issue>-a` | Retarget to main after A merges |
| C | `feat/<issue>-b` | Retarget to main after B merges |

## Creating the Chain

### Step 1: Create PR A
```bash
git checkout main
git pull origin main
git checkout -b feat/$ISSUE-a

# Make foundation changes
git add .
git commit -m "feat(#$ISSUE): add foundation utilities"

git push -u origin feat/$ISSUE-a
gh pr create --base main \
  --title "[A] #$ISSUE: Foundation - Add utilities" \
  --body "$(cat pr_body.md)"
```

### Step 2: Create PR B (stacked on A)
```bash
git checkout feat/$ISSUE-a
git checkout -b feat/$ISSUE-b

# Make feature changes
git add .
git commit -m "feat(#$ISSUE): implement main feature"

git push -u origin feat/$ISSUE-b
gh pr create --base feat/$ISSUE-a \
  --title "[B] #$ISSUE: Feature - Implement logic" \
  --body "$(cat pr_body.md)"
```

### Step 3: Create PR C (stacked on B)
```bash
git checkout feat/$ISSUE-b
git checkout -b feat/$ISSUE-c

# Make integration changes
git add .
git commit -m "feat(#$ISSUE): add tests and docs"

git push -u origin feat/$ISSUE-c
gh pr create --base feat/$ISSUE-b \
  --title "[C] #$ISSUE: Integration - Tests & docs" \
  --body "$(cat pr_body.md)"
```

## Merge Order

**CRITICAL**: Always merge in order A → B → C

```bash
# After A is approved
gh pr merge <PR-A> --squash

# Retarget B to main
gh pr edit <PR-B> --base main

# After B is approved
gh pr merge <PR-B> --squash

# Retarget C to main
gh pr edit <PR-C> --base main

# After C is approved
gh pr merge <PR-C> --squash
```

## Handling Updates

### When main is updated
```bash
# Update A
git checkout feat/$ISSUE-a
git rebase main
git push --force-with-lease

# Update B (rebase on updated A)
git checkout feat/$ISSUE-b
git rebase feat/$ISSUE-a
git push --force-with-lease

# Update C (rebase on updated B)
git checkout feat/$ISSUE-c
git rebase feat/$ISSUE-b
git push --force-with-lease
```

### When PR A has changes
```bash
# B and C must be rebased
git checkout feat/$ISSUE-b
git rebase feat/$ISSUE-a
git push --force-with-lease

git checkout feat/$ISSUE-c
git rebase feat/$ISSUE-b
git push --force-with-lease
```

## Validation

Before creating chain:
- [ ] A can be merged independently (no broken state)
- [ ] B only depends on A (not on C)
- [ ] C only depends on B
- [ ] No circular dependencies
- [ ] Each PR passes CI independently
