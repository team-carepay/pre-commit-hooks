# pre-commit-hooks

## check-jira-issue
This hook will verify if the current branch name refers to a valid issue in JIRA.
This assumes a naming convention of feature/KEY-999-xxx-yyy-zzz

Arguments:
* -u / --auth
  Used to specify credentials for private JIRA accounts. Specify with `user:password`
* -h / --host
  Used to specify JIRA hostname. Default is jira.atlassian.net

## checkstyle
This hook will run checkstyle on the changed files. Any arguments passed will to added to the checkstyle commandline.

Arguments:
* -c \<config-file>
  Used to specify the location of the config file.

## prepend-jira-issue
Script used in `prepare-commit-msg` phase.  If the current commit msg does not contain a reference to a JIRA issue, it will prepend the JIRA issue reference. Uses the branch name to detectmine the key.

Example .pre-commit-config.yaml:
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

```uml
@startuml
title Authentication Sequence
    Alice->Bob: Authentication Request
    note right of Bob: Bob thinks about it
    Bob->Alice: Authentication Response
@enduml
```
