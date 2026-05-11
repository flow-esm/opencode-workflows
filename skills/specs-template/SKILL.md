---
name: specs-template
description: >
  Template and rules for writing structured, agent-ready specifications
  from an issue, prompt, or refined discussion. Use this skill whenever
  you are producing specs that downstream planner/test-dev/code-dev
  agents will consume. Specs MUST follow this template exactly so that
  parsing and downstream automation are reliable.
license: MIT
---

# Specs template

Specs are agent-ready when a planner can decompose them into independent
features without asking follow-ups, and a test-dev can write tests
covering each acceptance criterion verbatim.

## Required sections (in this order)

```markdown
# Specs: <one-line title that mirrors the issue title>

## 1. Context
1–3 sentences. Why this work exists. Link the originating issue/PR.

## 2. Goal
A single sentence stating the outcome. No "should" weasels.

## 3. In scope
Bulleted list. Each bullet is one observable behavior or artifact.

## 4. Out of scope
Bulleted list. Explicit non-goals so implementation does not creep.

## 5. Acceptance criteria
Numbered list of testable statements, each phrased so a single test
case can verify it. Use the form:

  AC-N: GIVEN <state> WHEN <action> THEN <observable result>

## 6. Edge cases to test
Bulleted list. Each bullet names ONE edge case and the expected
behavior. Examples to consider: empty inputs, max-size inputs, unicode,
timezone boundaries, network failures, concurrent access, idempotency
on retry, malformed input, missing optional fields.

## 7. Files to add / modify / delete
Three subsections, each a bulleted list of repo-relative paths:
  - Add: <path> — <one-line purpose>
  - Modify: <path> — <one-line description of change>
  - Delete: <path> — <reason>

If a path is uncertain, name the *directory* and add "(exact filename
to be decided by code-dev)".

## 8. Open questions
Empty if specs are converged. If non-empty, the specs are NOT ready —
emit clarifying-questions instead of specs.

## 9. References
Links to issue, related PRs, prior discussions, or external docs.
```

## Rules

- Every acceptance criterion must be independently testable. If it
  isn't, split it.
- "Should support unicode" is not an AC. "GIVEN a username containing
  non-ASCII characters WHEN the user registers THEN the username is
  stored byte-identical" is.
- Do not number sections differently. Do not skip sections; if a
  section is genuinely empty, write "None" under it.
- Out-of-scope items are mandatory. The test-dev and code-dev agents
  read this section to know what NOT to build.
- The "Files to add/modify/delete" section is mandatory even when the
  set is small — it is the planner's primary input for feature
  decomposition.

## Anti-patterns

- Burying requirements in prose paragraphs instead of the AC list.
- Vague verbs: "handle", "manage", "support" without an observable.
- Mixing in/out-of-scope.
- Leaving "Open questions" non-empty and proceeding anyway.
