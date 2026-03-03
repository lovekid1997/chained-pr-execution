# PR Splitting Rules

## Core Principles

### 1. Atomic Changes
Each PR contains exactly ONE logical change:
- ✅ One refactor OR one feature OR one bugfix
- ❌ Refactor + feature mixed
- ❌ Multiple unrelated changes

### 2. Size Limits
- **Ideal**: 100-300 lines changed
- **Maximum**: 500 lines changed
- **If exceeded**: Split further

### 3. Layer Classification

| Layer | Type | Contains | Example |
|-------|------|----------|---------|
| A | Foundation | Shared utils, types, configs | New helper function, type definitions |
| B | Feature | Main implementation | Business logic, components |
| C | Integration | Wiring, tests, docs | Tests, exports, documentation |

## Splitting Algorithm

```
1. List all changes needed
2. Identify shared/foundation code → Layer A
3. Identify main feature code → Layer B
4. Identify tests/docs/wiring → Layer C
5. Check each layer size
6. If any layer > 500 lines → Split into A1, A2...
```

## Decision Tree

```
Is this a shared utility/type?
  └─ YES → Layer A
  └─ NO ↓

Is this main feature logic?
  └─ YES → Layer B
  └─ NO ↓

Is this test/doc/export?
  └─ YES → Layer C
```

## Anti-Patterns

### ❌ The Kitchen Sink PR
One PR with everything - hard to review, risky to merge.

### ❌ The Artificial Split
Splitting one function across 3 PRs - creates broken intermediate states.

### ❌ The Circular Dependency
A depends on B, B depends on A - impossible to merge.

## Examples

### Good Split
```
Issue: Add user authentication

PR A: Add JWT utility functions
PR B: Implement auth middleware + routes
PR C: Add tests + update API docs
```

### Bad Split
```
Issue: Add user authentication

PR A: Add login route (incomplete without middleware)
PR B: Add middleware (incomplete without JWT utils)
PR C: Add JWT utils (should be first!)
```
