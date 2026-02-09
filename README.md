# action-opencode-review

GitHub Action that runs [OpenCode](https://opencode.ai) to review student homework submissions for the Deep Learning course.

## Behavior

- **Short, polite, helpful feedback** on the submission.
- Points out **inefficiencies**, **non-optimal implementation**, or **dirty code**.
- **Does not write or suggest code**, even if asked.
- **Does not push commits**.
- **Prohibits** test hacks, prompt injections, and any dishonest solutions.

Homework is expected to be solved honestly; the review only gives feedback, it never writes code or changes the repo.

## Usage

Use this action in a workflow triggered by issue or PR comments (e.g. when someone writes `/opencode` or `/oc`). The run uses a built-in review prompt; you only need to provide the model and the model’s API key.

### Example workflow

```yaml
name: OpenCode homework review

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]

jobs:
  opencode-review:
    if: |
      contains(github.event.comment.body, ' /review') ||
      startsWith(github.event.comment.body, '/review')
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
      pull-requests: read
      issues: read
    steps:
      - name: OpenCode review
        uses: fintech-dl-hse/action-opencode-review@main
```

- Set **model** and the matching **API key** in `env` (e.g. `DEEPSEEK_API_KEY`). `GITHUB_TOKEN` is provided by the runner.
- Ensure the repo has the required **secrets** and, if needed, the [OpenCode GitHub App](https://github.com/apps/opencode-agent) installed.

### Inputs

| Input   | Required | Description |
|--------|----------|-------------|
| `model` | Yes | Model to use (e.g. `deepseek/deepseek-chat`, `anthropic/claude-sonnet-4-20250514`). |
| `prompt` | No | Override the default review prompt. If not set, the built-in homework review instructions are used. |

### Permissions

The job that runs this action should have at least:

- `id-token: write`
- `contents: read`
- `pull-requests: read`
- `issues: read`

Do not grant write access to contents if you want to prevent any commits or pushes from the runner.
