# pre-commit-hooks

## check-jira-issue
This hook will verify if the current branch name refers to a valid issue in JIRA.
This assumes a naming convention of feature/KEY-999-xxx-yyy-zzz.

We recommend to store username and JIRA API token in the global `.gitconfig` file:
```
[user]
    email = user@host.com
    jira-api-token = xxxxxxxxxxxx
```

Arguments:
* `-u / --username` `(env: JIRA_USERNAME)`
  Username for JIRA (e.g. user@host.com). If not specified, the e-mail address in `~/.gitconfig` will be used
* `-p / --password` `(env: JIRA_PASSWORD)`
  Password or API Token for JIRA. If not specified, the property `jira-api-token` in `~/.gitconfig` will be used
* `--auth (env: JIRA_AUTH)`
  (deprecated) Used to specify credentials for private JIRA accounts. Specify with `username:password`
* `-h / --host` `(env: JIRA_HOSTNAME)`
  Used to specify JIRA hostname. Default is `jira.atlassian.net`

## checkstyle
This hook will run checkstyle on the changed files. Any arguments passed will to added to the checkstyle commandline.

Arguments:
* `-c \<config-file>`
  Used to specify the location of the config file.

## detekt
This hook will run detekt on the changed files. Any arguments passed will to added to the detekt commandline.

Arguments (a complete list can be found [here](https://detekt.github.io/detekt/cli.html)):
* `-c \<config-file>`
  Path to the config file (path/to/config.yml). Multiple configuration files can be specified with ',' or ';' as separator.
* `-ex [**/*.kts]`
  Globing patterns describing paths to exclude from the analysis.


## prepend-jira-issue
Script used in `prepare-commit-msg` phase.  If the current commit msg does not contain a reference to a JIRA issue, it will prepend the JIRA issue reference. Uses the branch name to detectmine the key.

Example `.pre-commit-config.yaml`:
```
repos:
  - repo: git@github.com:team-carepay/pre-commit-hooks
    rev: v1.0.8
    hooks:
      - id: checkstyle
        args: [ -c, https://carepaydev.bitbucket.io/checkstyle-1.1.xml ]
      - id: check-jira-issue
        args: [ --auth, user@host.com:ApiKeyGoesHere, --host, carepay.atlassian.net ]
      - id: prepend-jira-issue
```
