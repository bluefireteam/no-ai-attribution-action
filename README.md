# No AI Attribution Action

Keeps AI attribution out of your git history by failing pull requests that
contain it.

Using AI tools is allowed, this action doesn't stop anyone from using them.
The problem it solves is that the tools add trailers, footers and links to
commit messages and pull request descriptions, and when a pull request is
squash merged all of that ends up in the git history. Over time the history
gets polluted with lines that say nothing about the change itself, so the
action asks contributors to remove them before merging.

## Usage

Run the action on every pull request, and include `edited` so that the check
runs again when the description changes. The action doesn't need a checkout.

```yaml
name: No AI Attribution

on:
  pull_request:
    types:
      - opened
      - edited
      - reopened
      - synchronize

permissions:
  contents: read
  pull-requests: read

jobs:
  main:
    name: Check for AI attribution
    runs-on: ubuntu-latest
    steps:
      - uses: bluefireteam/no-ai-attribution-action@v1
```

When the check fails it adds an error annotation to the workflow run, so the
message is shown in the check summary of the pull request without having to
open the logs. The message says that using AI tools is fine and lists the lines
that have to be removed.

## What is checked

The description of the pull request, and the messages and authors of its
commits. The authors are included because GitHub adds them as trailers when
squash merging. Existing history on the default branch is not checked.

These lines are flagged:

- Trailers with the addresses that the tools use, for example
  `Co-authored-by: Claude <noreply@anthropic.com>`. Matching is done on the
  address rather than the name, so a person named Claude isn't flagged.
- Footers such as "Generated with" and "Made with" followed by a tool name.
- `claude.ai/code/session` links.

Prose that only mentions a tool is allowed.

## Parameters

| Parameter | Default        | Description                                          |
| --------- | -------------- | ---------------------------------------------------- |
| token     | `github.token` | The token used to read the pull request and commits. |
