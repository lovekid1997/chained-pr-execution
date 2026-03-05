# Issue Parsing Guide

## Expected Issue Structure

Issues should contain a HOW section with implementation steps:

```markdown
## Problem
<description of what needs to be solved>

## HOW
1. Step one description
2. Step two description
3. ...

## Acceptance Criteria
- [ ] Criteria 1
- [ ] Criteria 2
```

## Parsing Steps

### 1. Extract Metadata
```bash
gh issue view <number> --json title,body,labels,assignees
```

### 2. Identify HOW Section

Look for:
- `## HOW` or `## How` or `## Implementation`
- Numbered list of steps
- Code snippets showing approach

### 3. Map HOW to PR Layers

| HOW Step Type | Maps To |
|---------------|---------|
| "Add utility/helper" | Layer A |
| "Create type/interface" | Layer A |
| "Implement feature" | Layer B |
| "Add route/endpoint" | Layer B |
| "Write tests" | Layer C |
| "Update docs" | Layer C |

### 4. Create Execution Plan

From HOW, generate:

```json
{
  "issue": 123,
  "execution_plan": {
    "A": {
      "steps": ["HOW step 1", "HOW step 2"],
      "files": ["src/utils/...", "src/types/..."]
    },
    "B": {
      "steps": ["HOW step 3", "HOW step 4"],
      "files": ["src/features/...", "src/routes/..."]
    },
    "C": {
      "steps": ["HOW step 5"],
      "files": ["tests/...", "docs/..."]
    }
  }
}
```

## Example

### Issue
```markdown
## Problem
Need to add full-text search to the application.

## HOW
1. Create search utility functions (indexer, query parser)
2. Add SearchQuery and SearchResult types
3. Implement SearchService
4. Add /search API endpoint
5. Write integration tests
6. Update API documentation

## Acceptance Criteria
- [ ] Search returns results in < 100ms
- [ ] Supports fuzzy matching
- [ ] All search endpoints tested
```

### Parsed Plan
```
Layer A (Foundation):
- Step 1: Search utilities
- Step 2: Types

Layer B (Feature):
- Step 3: SearchService
- Step 4: API endpoint

Layer C (Integration):
- Step 5: Tests
- Step 6: Docs
```

## Validation

Before starting:
- [ ] HOW section exists and is clear
- [ ] Steps are numbered and actionable
- [ ] Dependencies between steps are identified
- [ ] Each step maps to exactly one layer
