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
Need to add JWT authentication to the API.

## HOW
1. Create JWT utility functions (sign, verify, decode)
2. Add TokenPayload and AuthConfig types
3. Implement authMiddleware
4. Add /login and /logout routes
5. Write integration tests
6. Update API documentation

## Acceptance Criteria
- [ ] JWT tokens expire after 1 hour
- [ ] Refresh tokens supported
- [ ] All auth routes tested
```

### Parsed Plan
```
Layer A (Foundation):
- Step 1: JWT utilities
- Step 2: Types

Layer B (Feature):
- Step 3: authMiddleware
- Step 4: Routes

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
