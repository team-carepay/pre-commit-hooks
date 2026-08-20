# pre-commit-hooks

Shared [pre-commit](https://pre-commit.com/) hooks for CarePay repositories.
See [CHANGELOG.md](CHANGELOG.md) for what changed between revisions.

## Installation

`append-jira-issue` runs at the `commit-msg` stage, which `pre-commit install`
does **not** wire up by default. Install both hook types:

```
pre-commit install -t pre-commit -t commit-msg
```

Or, so that a plain `pre-commit install` does it, add to your
`.pre-commit-config.yaml`:

```yaml
default_install_hook_types: [pre-commit, commit-msg]
```

Without `.git/hooks/commit-msg`, git never launches pre-commit at that point in
the commit and the hook silently never runs.

## check-jira-issue
This hook will verify if the current branch name refers to a valid issue in JIRA.
This assumes a naming convention of `feature/KEY-999-short-description`.

### Branch prefixes

| Prefix | Purpose |
| --- | --- |
| `feature/`, `feat/` | New features |
| `bugfix/`, `fix/` | Bug fixes |
| `hotfix/` | Urgent fixes |
| `release/` | Branches preparing a release |
| `chore/` | Everything else: dependencies, docs, build, CI, tests, refactors |

The JIRA key must be uppercase: `feature/CLAIM-123-short-description`. JIRA only
links a commit to an issue when the key appears in its uppercase form, and
`append-jira-issue` copies the key from the branch name verbatim.

Exceptions:
* `master`, `main` and a detached `HEAD` skip validation.
* `renovate/*` and `dependabot/*` skip validation. The bots name their own branches and there is no ticket to look up.
* Every `release/*` branch skips validation, whether or not it carries a key.
  Release branches are named after the version they prepare (`release/v1.2.0`),
  so there is usually no ticket to look up.

We recommend to store username and JIRA API token in the global `.gitconfig` file:
```
[user]
    email = user@host.com
    jira-api-token = xxxxxxxxxxxx
```

Arguments (a flag beats the environment variable, which beats the default):
* `-u / --username` `(env: JIRA_USERNAME)`
  Username for JIRA (e.g. user@host.com). If not specified, the e-mail address in `~/.gitconfig` will be used
* `-p / --password` `(env: JIRA_API_TOKEN)`
  Password or API Token for JIRA. If not specified, the property `jira-api-token` in `~/.gitconfig` will be used
* `--auth` `(env: JIRA_AUTH)`
  (deprecated) Used to specify credentials for private JIRA accounts. Specify with `username:password`.
  Prefer `~/.gitconfig` — passing this in `.pre-commit-config.yaml` commits your API token to the repository
* `-h / --host` `(env: JIRA_HOSTNAME)`
  Used to specify JIRA hostname. Default is `jira.atlassian.com`

## checkstyle
Downloads the checkstyle jar next to the hook scripts. Receives the changed filenames, and is intended to check them. Any arguments passed will be added to the checkstyle command line.

Arguments:
* `-c \<config-file>`
  Intended to specify the location of the config file.

## detekt
Downloads the detekt CLI and runs it over the project's Kotlin sources. Any arguments other than the ones below are added to the detekt commandline.

Arguments (a complete list can be found [here](https://detekt.github.io/detekt/cli.html)):
* `-c \<config-file>`
  Path to the config file (path/to/config.yml). Multiple configuration files can be specified with ',' or ';' as separator.
* `-ex [**/*.kts]`
  Globing patterns describing paths to exclude from the analysis.


## append-jira-issue
Script used in the `commit-msg` phase. If the commit message does not already
reference a JIRA issue, the key is taken from the branch name and added as a
[Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/#specification)
footer:

```
docs: adjust ci.md

Refs: CLAIM-123
```

The subject is never modified. Putting the key in front of it would displace the
conventional-commit type, and GitHub prefills the pull request title from the
commit subject on single-commit pull requests, so the semantic title check would
reject it.

Accepts the same prefixes as [check-jira-issue](#branch-prefixes). The message is
left alone when the key is already referenced anywhere outside git's comment
block — so writing `fix(CLAIM-123): add retry` yourself does not get a duplicate
footer — and when the subject is one git wrote itself (`Merge`, `Revert`,
`fixup!`, `squash!`, `amend!`) or the branch carries no key (trunk, `renovate/*`,
`dependabot/*`, `release/*`).

The footer is inserted with `git interpret-trailers`, so it joins an existing
trailer block and lands before git's comment block and before a `-v` scissors
line.

Example `.pre-commit-config.yaml`:
```yaml
default_install_hook_types: [pre-commit, commit-msg]

repos:
  - repo: git@github.com:team-carepay/pre-commit-hooks
    rev: v2.0.0
    hooks:
      - id: checkstyle
        args: [ -c, https://carepaydev.bitbucket.io/checkstyle-1.1.xml ]
      - id: check-jira-issue
        args: [ --host, carepay.atlassian.net ]
      - id: append-jira-issue
```
