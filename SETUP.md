# Setup guide

This guide walks you from zero to a working `/opendev` invocation in a
target repo.

## Prerequisites

- An opencode-go subscription (for `opencode-go/deepseek-v4-pro` and
  `opencode-go/deepseek-v4-flash` model access).
- A GitHub user account named `opencode-agent` (or another name — adjust
  the `assigned.yml` wrapper if you choose differently). This user is
  the one you'll assign issues to in order to trigger the assigned flow.
- Either:
  - The opencode GitHub App installed on each target repo (recommended;
    commits and comments appear as the app), OR
  - The default `GITHUB_TOKEN` (simpler; commits appear as
    `github-actions[bot]`).

## Step 1: Create the public `opencode-workflows` repo

1. Create a public repo under your account or org, named exactly
   `opencode-workflows`.
2. Push the contents of this directory tree to it.
3. Tag a release: `git tag v1 && git push origin v1`.

> The reusable workflows reference `OWNER/opencode-workflows@v1`; you
> can move the `v1` tag forward as you iterate. Consumers always pin to
> a tag, so they're insulated from your `main`-branch churn.

## Step 2: Configure the public repo's permissions

In the public `opencode-workflows` repo, go to:
Settings → Actions → General → "Access" and select:

> Accessible from repositories owned by the user '<owner>'

(or "Accessible from repositories in the '<org>' organization" if it's
under an org). This allows your private/other repos to invoke the
reusable workflows.

## Step 3: Set up your first consumer repo

In the target repo:

1. Copy the files from `consumer-wrappers/` (this directory) into
   `.github/workflows/`.
2. In each file, replace the `OWNER` placeholder with the account/org
   that hosts `opencode-workflows`.
3. Add the secret `OPENCODE_API_KEY` (Settings → Secrets and variables
   → Actions → Secrets). If you're using the opencode GitHub App for
   auth instead, follow the opencode docs.
4. (Optional) Add repository variables to override path globs:
   - `OPENCODE_TEST_GLOBS`
   - `OPENCODE_CODE_GLOBS`
5. (Optional but recommended) Create project-specific style skills:

   ```
   .opencode/skills/code-style/SKILL.md
   .opencode/skills/test-style/SKILL.md
   ```

   These are loaded automatically by code-dev / test-dev / reviewer and
   are MANDATORY by the orchestrator instructions when present. Without
   them, the agents fall back to language defaults and say so in their
   output.

## Step 4: Try it

1. Open an issue in your consumer repo.
2. Comment `/openspecs`.
3. Wait for the bot to either ask clarifying questions (answer them and
   the next comment will retrigger the workflow) or post specs.
4. When you're satisfied with the specs, comment `/opendev` on the same
   issue. The pipeline will create a branch, decompose the work, run
   test-dev / code-dev for each feature in parallel, and post a review
   summary plus full report.

For the assigned flow: assign an issue to the `opencode-agent` user.
That auto-creates a branch, a draft PR, and posts specs on the PR. When
you're happy with the specs, comment `/opendev` on the PR.

## How state is persisted across workflow runs

GitHub Actions has no resumable workflows. Each run is a fresh process.
Workflows that span multiple user interactions (`/openspecs`,
`/openrefine`, `/opendiscuss`, the assigned flow) work like this:

1. The agent posts a comment that ends with a hidden HTML state marker:
   `<!-- opencode-state workflow=<name> phase=<phase> round=<n> run=<id> -->`
2. The user's reply triggers a fresh workflow run.
3. The orchestrator reads the issue thread, finds the latest marker,
   and decides whether to ask the next question or converge.

You don't need to manage anything for this — the orchestrator agents
handle it. The marker format is documented inline in each orchestrator.

## How `/opendev` runs in parallel

`/opendev` is a single workflow run with three jobs:

```
plan ──▶ feature (matrix per feature) ──▶ review
```

The `plan` job invokes the planner subagent, which emits a JSON array
of features. That array becomes a `strategy.matrix` for the `feature`
job, so each feature runs in its own runner with `test-dev` then
`code-dev` sequentially. The `review` job runs once after all matrix
jobs complete (`if: always()` so partial failures still get reviewed).

If a feature fails (test-dev couldn't write tests, or code-dev couldn't
make them green), it's marked failed and the others continue. The final
review summary lists pass/fail per feature with a one-line failure note.

## Concurrency

Each reusable workflow declares
`concurrency.group: <command>-<issue_number>` so two concurrent runs
of the same command on the same issue queue rather than collide.
Different commands on the same issue can run concurrently.

## Auth notes

- Each reusable workflow declares the permissions it needs at job level.
  The consumer wrappers also set top-level permissions to match.
- `secrets: inherit` passes through `OPENCODE_API_KEY` and the auto
  `GITHUB_TOKEN`.
- If you use the opencode GitHub App, you can drop the
  `OPENCODE_API_KEY: ${{ secrets.OPENCODE_API_KEY }}` env line from the
  `anomalyco/opencode/github` step in the reusable workflows — the
  action will auth via OIDC. Verify against current opencode docs.

## Model identifiers

The agents use:

- `opencode-go/deepseek-v4-pro` (specs-creator, reviewer, /opencode default,
  opendev plan & review orchestrators)
- `opencode-go/deepseek-v4-flash` (planner, moderator, adversarial,
  test-dev, code-dev, literature-researcher, opendev feature orchestrator)

Per-agent variant (`max` for the heavier roles, `medium` for execution
roles) is recorded in each agent's `metadata.variant`. If your
opencode-go plan exposes the variant via a different mechanism (e.g. a
suffix on the model string), update the `model:` field in each agent
file accordingly.

## Adding a new workflow

1. Add a reusable workflow under `.github/workflows/<name>.yml`.
2. Add an orchestrator agent under `agents/orchestrator-<name>.md`.
3. (Optional) Add new subagents.
4. Add a consumer wrapper under `consumer-wrappers/<name>.yml`.
5. Bump the tag and update consumers.

## Troubleshooting

- **`workflow_call` not found** → check Step 2 (organisation access
  setting on the public repo).
- **Agent not loaded** → confirm `agents/<name>.md` exists in the tag
  consumers reference; the setup composite copies them at runtime.
- **Path-glob enforcement bypassed** → the orchestrator path-checks
  before approving writes. If a subagent wrote outside its globs, the
  orchestrator reverts and posts a comment.
- **Comment posted but no follow-up** → check the state marker is
  exactly `<!-- opencode-state workflow=... phase=... -->` (HTML
  comment, single line, lowercase keys). Orchestrators parse strictly.
