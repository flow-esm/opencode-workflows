# opencode-workflows

Reusable GitHub workflows, agents, and skills for delegating work to
[opencode](https://opencode.ai) from issues and pull requests.

## What's here

```
.
├── .github/
│   ├── workflows/         # Reusable workflows (workflow_call entrypoints)
│   │   ├── opendev.yml
│   │   ├── openspecs.yml
│   │   ├── openreview.yml
│   │   ├── openrefine.yml
│   │   ├── opendiscuss.yml
│   │   ├── opencode.yml
│   │   └── assigned.yml
│   └── actions/setup/     # Composite action shared by all workflows
├── agents/                # Markdown agent definitions, copied into
│                          #   .opencode/agent/ at runtime
├── skills/                # Skills (specs-template, review-report,
│                          #   literature-report), copied into skill/
├── consumer-wrappers/     # Files you drop into your own repos
└── SETUP.md               # Setup guide
```

## Workflows at a glance

| Slash command | Surfaces        | Purpose                                                    |
|---------------|-----------------|------------------------------------------------------------|
| `/opendev`    | issues + PRs    | TDD pipeline: planner → parallel test-dev/code-dev → review|
| `/openspecs`  | issues only     | Convert issue into agent-ready specs                       |
| `/openreview` | PRs only        | Review changes; recommendations only                       |
| `/openrefine` | issues only     | Multi-round Socratic refinement                            |
| `/opendiscuss`| issues only     | Convener + adversarial discussion                          |
| `/opencode`   | issues + PRs    | Generic opencode invocation with the default Build agent   |
| (assignment)  | issue assigned  | Branch + draft PR + specs-creator chain                    |

## Quickstart

1. Read [SETUP.md](./SETUP.md).
2. Drop the files in `consumer-wrappers/` into a target repo's
   `.github/workflows/` directory (rename `OWNER` placeholder).
3. Set the `OPENCODE_API_KEY` secret in that repo.
4. Open an issue and comment `/openspecs`.

## Versioning

Reusable workflows are pinned by tag (e.g. `@v1`). Consumer wrappers
reference these tags. Update by changing the tag in your wrapper.

## Customisation

- Override path globs per repo via the variables `OPENCODE_TEST_GLOBS`
  and `OPENCODE_CODE_GLOBS` (Settings → Secrets and variables → Actions →
  Variables).
- Add project-specific `code-style` and `test-style` skills under
  `.opencode/skills/code-style/SKILL.md` and
  `.opencode/skills/test-style/SKILL.md` in your consumer repos.
  These are loaded by code-dev / test-dev / reviewer.
