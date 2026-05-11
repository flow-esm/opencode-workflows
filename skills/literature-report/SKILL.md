---
name: literature-report
description: >
  Template and rules for writing literature review reports that ground
  subsequent reasoning in actual cited publications. Use this skill
  whenever the literature-researcher agent produces output. Reports
  MUST use this format so the moderator and adversarial agents can rely
  on the citation structure.
license: MIT
---

# Literature report template

## Required structure

```markdown
# Literature on <topic>

## Framing
2–4 sentences. What is the topic. Which schools of thought exist.
Where does open disagreement lie. If the field is very new, say so.

## Sources

### [S1] <Author, Year — short title>
**Citation:** <full reference, ideally journal/conference; mark
preprints as such>
**Link:** <URL>
**Why it matters:** 2–4 sentences. What this source contributes
specifically to the topic. Distinguish established consensus from
contested claims. If the source disagrees with an earlier [Sn], say so.

### [S2] ...
(repeat)

## Synthesis
2–4 sentences. Tie the sources back to the question that motivated the
search. Identify what is well-established, what is contested, and what
remains open. Avoid overclaiming.

## Caveats
Anything you could not find, search angles that turned up nothing,
known biases in the corpus you searched, or topics where the literature
is too sparse to cite responsibly.
```

## Rules

- Cite 5–10 sources. Fewer if the field is genuinely small.
- Prefer journal/conference papers over preprints when both exist for
  the same idea. Mark preprints explicitly.
- NEVER fabricate a citation. If you cannot find a real source for a
  claim, drop the claim.
- Paraphrase. Do NOT paste long quotations from sources.
- Use [Sn] back-references when one source contradicts or builds on
  another, so downstream agents can trace dependencies.
- The "Caveats" section is mandatory. Empty caveats = "I lied about
  comprehensiveness."

## Anti-patterns

- Listing sources without a "Why it matters" paragraph.
- Citing the same author repeatedly to inflate count.
- Treating a single survey paper as definitive.
- Leaving "Synthesis" as a restatement of the framing.
