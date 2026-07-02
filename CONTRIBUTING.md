# Contributing

This document defines our branching, commit, and pull request conventions. It exists so that
version bumps, changelogs, and release notes can be generated automatically from PR history —
following these conventions is not optional style preference, it's what our tooling
(`commitlint`, `release-please`) depends on to work correctly.

## Branch naming

Prefix branches with the type of work, matching the commit types below:

```
feature/bulk-tagging
bugfix/null-check-empty-project
hotfix/prod-auth-crash
chore/upgrade-node-20
```

This is a human-facing convention for scanning branch lists — it doesn't drive any automation.

## Commit messages (Conventional Commits)

All **PR titles** must follow this format (this is enforced by CI — see below):

```
<type>(<scope>): <short imperative description>
```

**Examples:**

```
feat(app): add dark mode toggle
fix(cms): correct null pointer on empty list view
chore(infra): upgrade node to 20.x
refactor(backend): extract auth middleware into shared util
```

### Rules

- **Type is required** and must be one of:

  | Type | Use for | Triggers release? |
  |------|---------|---|
  | `feat` | New feature | Minor version bump |
  | `fix` | Bug fix | Patch version bump |
  | `perf` | Performance improvement | Patch version bump |
  | `refactor` | Code change with no behavior change | No |
  | `chore` | Tooling, deps, config | No |
  | `docs` | Documentation only | No |
  | `test` | Adding or fixing tests | No |
  | `build` | Build system / dependencies | No |
  | `ci` | CI/CD configuration | No |

- **Scope should be included** whenever the change is tied to one area, matching our project's
  Area field: `app`, `cms`, `backend`, `infra`, `mixed`. Omit scope only for genuinely
  cross-cutting changes.
- **Lowercase subject**, no trailing period.
- **Breaking changes**: append `!` after the type/scope (e.g. `feat(api)!: remove legacy v1 routes`)
  and include a `BREAKING CHANGE:` footer describing the impact. This triggers a major version bump.

### Why the PR title specifically matters

We **squash-merge exclusively** — merge commits and rebase merging are disabled at the repo level.
This means the PR title becomes the *only* commit message that survives onto `main`, and it is
what `release-please` parses to compute version bumps and generate the changelog. Individual
commit messages within your branch are not enforced as strictly, but writing them well is still
good practice — they're preserved in the squash commit body for future archaeology.

## One PR, one concern

Each PR should address a single logical change. If you discover an unrelated bug while working
on a feature, split it into its own branch/PR rather than bundling it in — a squashed commit that
mixes a `feat` and an unrelated `fix` means that fix has no changelog entry and can't be reverted
independently.

## Merge strategy

- **Squash and merge only.** Merge commits and rebase merging are disabled.
- Default commit message option: **"Pull request title and commit details."** The title becomes
  the squash commit header (must follow the convention above); individual commit messages from
  the branch are preserved in the commit body for reference.

## Enforcement

The following CI checks are required status checks and must pass before merging:

- **PR title lint** (`amannn/action-semantic-pull-request`) — validates the PR title against the
  Conventional Commits format and scope enum on every title edit, not just at open.
- **Commit lint** (`wagoid/commitlint-github-action`) — dev-time feedback on individual commits
  within the branch.

## Checklist before opening a PR

- [ ] Branch name follows the `type/description` convention
- [ ] PR title follows Conventional Commits format (`type(scope): description`)
- [ ] PR addresses a single concern (no unrelated fixes bundled in)
- [ ] Tests updated/added if applicable