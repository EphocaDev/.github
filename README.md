# EphocaDev defaults

This repository is public. Nothing secret belongs in it.

## Pull request template

`PULL_REQUEST_TEMPLATE.md` is the pull request template for every EphocaDev repository that has
no template of its own. Change it through a pull request (BS-1475).

## Shared workflows

A workflow that several repositories need lives here once, under `.github/workflows/`, and each
repository calls it (BS-1546). A fix made here reaches every caller.

| Workflow | What it does | Check name in a ruleset |
| :-- | :-- | :-- |
| `jira-key.yml` | Fails a pull request with no `MWD-###` or `BS-###` in its branch name or title | `jira-key / check` |

### Calling one

The caller keeps the trigger; the shared file keeps the logic. This is the whole of a
repository's `.github/workflows/jira-key.yml`:

```yaml
name: Jira key

on:
  pull_request:
    # A repository with a `production` branch checks pull requests into main only: its
    # promotion PR has no key in its title. A repository without one drops this line.
    branches: [main]
    types: [opened, edited, reopened, synchronize]

jobs:
  jira-key:
    uses: EphocaDev/.github/.github/workflows/jira-key.yml@<commit SHA>
```

Pin the call to a commit SHA of this repository, never to `main`: a caller on `main` would run
a change here before anyone reviewed it in that repository.

### Changing one

Change it through a pull request here, then move each caller's pin in its own pull request. A
job id in a shared workflow is part of the check name its callers' rulesets require, so renaming
one means updating those rulesets in the same step.
