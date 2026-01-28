---
name: git-diff-review
description: "리뷰해줘", "분석해줘", "파악해줘" 요청 시 사용. Git diff and PR review specialist. Analyzes code changes between commits or in pull requests, focusing on frontend quality, security, and performance.
allowed-tools: Bash, Read, Grep, Glob
model: inherit
---

# Git Diff & PR Review Skill

Git diff와 PR 변경사항을 분석하여 프론트엔드 품질, 보안, 성능을 중심으로 리뷰하는 Skill입니다.

---

## When to Use This Skill

Use this skill when the user requests:

- "Review this PR"
- "Review my changes"
- "Review the diff"
- "Review commit {hash}"
- "What changed in this PR?"

**DO NOT** use for:

- Reviewing entire files without context of changes
- General code questions unrelated to diffs

---

## Core Process

### Step 1: Identify Review Type

Determine what needs to be reviewed:

- **PR Review**: GitHub Pull Request
- **Commit Review**: Specific commit(s)
- **Working Changes**: Current unstaged/staged changes
- **Branch Comparison**: Compare two branches

### Step 2: Fetch Changes

Based on review type, execute appropriate command:

```bash
# PR review (current branch vs base)
git diff origin/develop...HEAD

# Specific commits
git diff {prev-hash}..{next-hash}

# Last N commits
git diff HEAD~5..HEAD

# Current working changes
git diff  # unstaged
git diff --cached  # staged

# With file list
git diff --name-only origin/develop...HEAD

# With stats
git diff --stat origin/develop...HEAD
```

### Step 3: Parse Diff Output

Analyze the diff structure:

```diff
diff --git a/src/components/Example.tsx b/src/components/Example.tsx
index abc123..def456 100644
--- a/src/components/Example.tsx
+++ b/src/components/Example.tsx
@@ -10,7 +10,7 @@ export function Example() {
   const { data } = useQuery(GET_DATA);

-  {items && <List items={items} />}
+  {items?.length > 0 && <List items={items} />}
```

**Key elements to identify:**

- **File path**: `src/components/Example.tsx`
- **Line numbers**: `@@ -10,7 +10,7 @@`
- **Removed lines**: Lines starting with `-`
- **Added lines**: Lines starting with `+`
- **Context lines**: Unchanged lines for reference

### Step 4: Apply Review Checklist

For each changed file, check against these criteria:

#### Frontend Quality

- [ ] **React Best Practices**

  - Proper hook usage (useState, useEffect, useMemo, useCallback)
  - Correct dependency arrays
  - No unnecessary re-renders
  - Component composition over prop drilling

- [ ] **TypeScript Type Safety**

  - No `any` types added
  - Proper type definitions
  - Generic types used correctly
  - Type narrowing and guards

- [ ] **Apollo Client / GraphQL** (if applicable)

  - Query/mutation patterns correct
  - Cache updates handled properly
  - Error handling present
  - Loading states managed

- [ ] **Recoil State Management** (if applicable)
  - Atom keys unique
  - Selectors pure functions
  - Proper state scope

#### Code Quality

- [ ] **Readability**

  - Clear variable/function names
  - No magic numbers (use named constants)
  - Complex logic abstracted
  - Comments where necessary (not obvious code)

- [ ] **Maintainability**
  - DRY principle followed
  - Single Responsibility Principle
  - No premature optimization
  - Proper error handling

#### Performance

- [ ] **React Performance**

  - useMemo/useCallback used appropriately
  - No expensive operations in render
  - Lazy loading for heavy components
  - Proper key props in lists

- [ ] **Bundle Size**
  - No large unnecessary dependencies added
  - Tree-shaking friendly imports
  - Code splitting considered for large features

#### Security

- [ ] **Input Validation**

  - User input validated (Yup schemas)
  - XSS prevention (proper escaping)
  - No eval() or dangerous patterns

- [ ] **Authentication & Authorization**
  - Protected routes/components
  - Token handling secure
  - No secrets in code

#### Accessibility

- [ ] **Semantic HTML**

  - Proper heading hierarchy
  - Alt text for images
  - ARIA labels where needed

- [ ] **Keyboard Navigation**
  - Focusable interactive elements
  - Logical tab order

#### MUI & Styling

- [ ] **MUI Patterns**

  - Consistent sx prop usage
  - Theme values used (not hardcoded colors)
  - Responsive design considered

- [ ] **Emotion CSS-in-JS**
  - Styled components follow conventions
  - No inline styles unless necessary

### Step 5: Generate Structured Review

Format the review in a clear, actionable structure:

````markdown
## 📊 Review Summary

**Review Type**: [PR / Commit / Working Changes]
**Files Changed**: [number]
**Lines Added**: [number]
**Lines Deleted**: [number]

---

## 📁 Changed Files

1. `src/components/order/OrderList.tsx` (+12, -5)
2. `src/hooks/useOrder.ts` (+3, -1)

---

## ✅ Good Changes

### src/components/order/OrderList.tsx:45

```diff
- {items && <List items={items} />}
+ {items?.length > 0 && <List items={items} />}
```
````

✅ **Improvement**: Added proper array length check with optional chaining

- Handles undefined/null safely
- Prevents rendering with empty array
- Follows project patterns

---

## ⚠️ Issues Found

### src/hooks/useOrder.ts:23

```diff
+ const data: any = await fetchOrder()
```

⚠️ **Type Safety Issue**: Using `any` type

- **Problem**: Loses type safety
- **Impact**: Runtime errors possible
- **Fix**: Define proper interface

```typescript
interface Order {
  id: string;
  items: OrderItem[];
  total: number;
}
const data: Order = await fetchOrder();
```

---

## 💡 Suggestions

1. **Testing**: Consider adding unit tests for the new logic in OrderList.tsx
2. **Performance**: The new filter operation might benefit from useMemo if the list is large
3. **Documentation**: Update CLAUDE.md if this introduces a new pattern

---

## 🎯 Overall Assessment

**Status**: [✅ Approved / ⚠️ Needs Changes / 🔴 Blocking Issues]

**Summary**: [1-2 sentence overall evaluation]

**Action Items**:

- [ ] Fix type safety issue in useOrder.ts
- [ ] Add unit tests
- [ ] Update documentation

````

---

## GitHub PR Review (Optional)

If the user wants to post review to GitHub:

```bash
# View PR details
gh pr view {number}

# Get PR diff
gh pr diff {number}

# Post comment
gh pr comment {number} --body "Review feedback here"

# Submit review
gh pr review {number} --approve
gh pr review {number} --comment --body "Feedback"
gh pr review {number} --request-changes --body "Issues found"

# Comment on specific line
gh pr review {number} \
  --comment \
  --body-file review.md
````

**Interactive Options**:

1. **View Only**: Display review in chat (default)
2. **Post Comment**: Add comment to PR
3. **Submit Review**: Approve/Request Changes/Comment

---

## Project-Specific Guidelines (Sirloin OMS)

### Always Check

**GraphQL Changes**:

```bash
# If .graphql files changed
git diff origin/develop...HEAD -- "*.graphql"

# Remind to run codegen
echo "⚠️ GraphQL changed - run: yarn codegen"
```

**Recoil State**:

```bash
# If recoil atoms changed
git diff origin/develop...HEAD -- "**/recoil/**"

# Check for atom key uniqueness
```

**MUI Components**:

- Consistent sx prop usage
- Theme values used
- No hardcoded colors/spacing

**Form Validation**:

- Yup schemas defined
- Error messages in Korean
- Proper validation rules

---

## Examples

### Example 1: Simple PR Review

**User Request**: "PR 리뷰해줘"

**Action**:

```bash
git diff origin/develop...HEAD --stat
git diff origin/develop...HEAD
```

**Analysis**:

- 3 files changed
- Added optional chaining
- Improved type safety
- No issues found

**Output**: "✅ LGTM - 좋은 개선입니다. 머지 가능합니다."

---

### Example 2: Commit Review with Issues

**User Request**: "최근 커밋 리뷰해줘"

**Action**:

```bash
git diff HEAD~1..HEAD
```

**Analysis**:

- Added new feature
- Found: `any` type usage
- Found: Missing error handling

**Output**:

```markdown
⚠️ Needs Changes

Issues:

1. Type safety: Replace `any` with proper interface
2. Error handling: Add try-catch in async function

Fix these before merging.
```

---

## Integration with Frontend Design Guide

This skill always applies the **frontend-design-guide** principles:

1. **Readability**: Check for magic numbers, complex ternaries, named conditions
2. **Predictability**: Verify consistent return types, no hidden side effects
3. **Cohesion**: Ensure related code is together, proper domain organization
4. **Coupling**: Look for props drilling, overly broad state management

---

## Response Format

Always structure diff reviews as:

```markdown
## 📊 Review Summary

[Stats and overview]

## 📁 Changed Files

[List with line counts]

## ✅ Good Changes

[Positive findings with explanations]

## ⚠️ Issues Found

[Problems with severity and fixes]

## 💡 Suggestions

[Optional improvements]

## 🎯 Overall Assessment

[Status and action items]
```

---

## Advanced Usage

### Multi-Commit Review

```bash
# Review range of commits
git log --oneline -10
git diff {start-hash}..{end-hash}
```

### Specific File Review

```bash
# Only review certain files
git diff origin/develop...HEAD -- src/components/**/*.tsx
```

### Ignore Whitespace

```bash
# Ignore whitespace changes
git diff -w origin/develop...HEAD
```

---

## Summary

이 Skill은 다음을 수행합니다:

1. **Context Detection**: 사용자 요청에서 리뷰 타입 파악
2. **Diff Extraction**: 적절한 git 명령어로 변경사항 추출
3. **Comprehensive Analysis**: Frontend 체크리스트 적용
4. **Structured Feedback**: 명확하고 실행 가능한 피드백 생성
5. **Project Alignment**: Sirloin OMS 프로젝트 기준 준수

**Key Principles**:

- Focus on **changes**, not entire files
- Provide **actionable** feedback
- Follow **frontend-design-guide** standards
- Generate **structured**, easy-to-read output
