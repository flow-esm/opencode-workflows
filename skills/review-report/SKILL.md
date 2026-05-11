---
name: review-report
description: >
  Template and rules for writing structured code review reports. Use
  this skill whenever you are producing a review of a PR or feature
  branch. The reviewer agent and any orchestrator that posts review
  output MUST follow this format so that severity classification and
  recommendations are consistent and machine-parseable.
license: MIT
---

# Review report template

A review report has TWO output artifacts, posted as separate comments:

1. **Summary comment** — short, scannable, status-icon checklist.
2. **Full report** — the structured document below.

## Severity scale (use these exact words)

- `blocking` — must be fixed before merge. Failing tests, security
  issues, silent data corruption, contract breakage.
- `high` — should be fixed before merge. Missed acceptance criteria,
  missing test coverage on new branches, regressions.
- `medium` — should be fixed soon. Style violations affecting
  readability, modest duplication, weak edge-case handling.
- `low` — nice-to-have. Minor naming, comment polish, micro-refactors.

## Summary comment format

```markdown
## <workflow> summary

**Verdict:** <one of: approve, approve-with-nits, request-changes, block>

### Features
- ✅ **<feature-id>** — <title>
- ❌ **<feature-id>** — <title> — <one-line failure note>

### Issues
- 🛑 blocking: <count>
- 🔴 high: <count>
- 🟠 medium: <count>
- 🟡 low: <count>

Full report posted as a follow-up comment.
```

If invoked from /openreview (no feature decomposition), omit the
"Features" section.

## Full report format

```markdown
# Review report — <PR/issue ref>

## 1. Test results
A code block of the test command and its summary line(s). If failures,
list them with file:line.

## 2. Spec adherence
For each acceptance criterion from the originating specs (if any):

  - AC-N: ✅ implemented in <file:line> | ❌ not implemented | ⚠️ partial — <note>

If no specs exist, write "No specs in thread; reviewing against issue
description."

## 3. Findings
Group by severity, in this order: blocking, high, medium, low.

For each finding:

### <severity>: <one-line title>
**Location:** `<path>:<lines>` (link to diff if possible)
**Observed:** <what the code does>
**Expected / suggested:** <what it should do, or a concrete change>
**Rationale:** <one or two sentences>

## 4. Refactor recommendations
Optional. Only if complexity, duplication, or structure issues warrant
it. Each item: location, problem, proposed shape, scope (in this PR vs.
follow-up).

## 5. Regressions
Behaviors that worked before this PR and now do not, OR behaviors
introduced that were not asked for. Empty if none.

## 6. Recommendation
One paragraph. Restate the verdict. List blocking/high items the user
must address before merge.
```

## Rules

- Every finding cites a file path and line range.
- Never invent issues. If you cannot point at a specific line, do not
  list a finding.
- Use the severity words exactly. No custom levels.
- The summary comment is a HEADLINE; the full report is the SOURCE OF
  TRUTH. The two must not contradict each other.
- If you ran into a tool failure (e.g., tests would not run), say so in
  section 1; do NOT proceed to make claims about test pass/fail.
