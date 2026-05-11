# Consumer wrappers

Drop these files into your repo's `.github/workflows/` directory.

Edit the `OWNER` placeholder at the top of each file to point to the
account or organization that hosts your `opencode-workflows` repo
(e.g. `tobias-bischoff`).

You can also configure these repository-level variables (Settings →
Secrets and variables → Actions → Variables) to override the default
test/code path globs:

  - `OPENCODE_TEST_GLOBS` (default: see workflow file)
  - `OPENCODE_CODE_GLOBS` (default: see workflow file)

Required secret (Settings → Secrets and variables → Actions → Secrets):

  - `OPENCODE_API_KEY` — your opencode-go API key

If you use the opencode GitHub App for auth instead of `OPENCODE_API_KEY`,
follow the opencode docs to install it on the repo and remove the
`OPENCODE_API_KEY` line from each wrapper.
