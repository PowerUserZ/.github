# `.github`

Shared defaults and reusable workflows for [PowerUserZ](https://github.com/PowerUserZ)
repositories.

## Reusable workflows

### Claude PR Review

Posts an automatic Claude review comment on every pull request. Uses a Claude
Max subscription via the `CLAUDE_CODE_OAUTH_TOKEN` secret — no API billing.

**Minimal caller workflow** (put in `.github/workflows/claude-review.yml` of
the consumer repo):

```yaml
name: Claude PR Review
on:
  pull_request:
    types: [opened, synchronize, reopened]
jobs:
  review:
    uses: PowerUserZ/.github/.github/workflows/claude-review.yml@main
    secrets:
      CLAUDE_CODE_OAUTH_TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

**Custom prompt / args:**

```yaml
    uses: PowerUserZ/.github/.github/workflows/claude-review.yml@main
    with:
      prompt: "Focus on bash safety and sentinel-based config edits."
      claude_args: "--max-turns 8"
    secrets:
      CLAUDE_CODE_OAUTH_TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

### Setup

1. Get an OAuth token once (expires in one year):
   ```bash
   claude setup-token
   ```
2. Add the token as a repo secret named `CLAUDE_CODE_OAUTH_TOKEN` in every
   consumer repo (GitHub does not support user-level secrets).
3. Drop the caller workflow above into the consumer repo's
   `.github/workflows/claude-review.yml`.

For one-command bootstrap, see `claude-review-enable` in the
[personal bashrc helper](https://gist.github.com/PowerUserZ) *(local to
the user's dotfiles)*.

### Limits

- **Fork PRs** do not trigger the review — GitHub does not forward secrets
  to forks. This is intentional.
- Max quota applies; the runner burn counts against monthly limits.
- Token expires in one year; rerun `claude setup-token` and update each
  repo's secret.
