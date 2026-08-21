# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed

- Bump the upstream `pre-commit/pre-commit-hooks` rev this repo lints itself with from `v2.5.0` to `v3.4.0`

### Fixed

- Fix this repo's own `.pre-commit-config.yaml`, which referred to the upstream hooks repository over `git://`. GitHub disabled that protocol in January 2022, so the clone hung and none of the nine configured hooks ran for contributors
- Add the missing final newline to `.editorconfig`, the first thing the restored `end-of-file-fixer` hook caught

## [2.0.0] - 2026-08-20

### Added

- Add `append-jira-issue`, replacing `prepend-jira-issue`. It adds the branch's JIRA key to the commit message as a `Refs: CLAIM-123` footer instead of prepending it to the subject.

### Changed

- **Breaking:** Adopt purpose prefixes for branch names: `feature/`, `feat/`, `bugfix/`, `fix/`, `hotfix/`, `release/`, `chore/`. `epic/` and `subtask/` are no longer accepted.
- **Breaking:** Require the JIRA key in a branch name to be uppercase and to start with a letter — `feature/CLAIM-123-add-retry`, not `feature/claim-123-add-retry`.
- Skip validation on `renovate/*` and `dependabot/*` branches instead of rejecting them. The bots name their own branches and there is no ticket to look up.
- Skip validation on every `release/*` branch, whether or not it carries a key. Release branches are named after the version they prepare, so there is usually no ticket to look up.

### Removed

- **Breaking:** Remove the `prepend-jira-issue` hook and its id, replaced by `append-jira-issue`. Rename the id in your `.pre-commit-config.yaml`.

## [1.3.1] - 2022-01-31

### Fixed

- Prepend `/usr/local/bin` to `PATH` so `check-jira-issue` finds `curl` and `jq` when the hook runs outside an interactive shell

## [1.3.0] - 2021-10-21

### Added

- Accept the `epic/`, `subtask/` and `release/` branch prefixes

### Changed

- Fail with a clear message when the JIRA API token is missing, rather than calling the JIRA API without credentials

## [1.2.0] - 2021-03-29

### Added

- Add `detekt` hook for Kotlin files

## [1.1.0] - 2021-02-06

### Security

- Read the JIRA username and API token from `~/.gitconfig` (`user.email` and `user.jira-api-token`), so credentials no longer have to be hard-coded into every consumer's `.pre-commit-config.yaml` where they were being committed and leaked (SUP-2440)

## [1.0.11] - 2020-10-16

### Fixed

- Iterate on the initial release across `1.0.1`–`1.0.11`: fix the hook manifest, resolve the checkstyle config path, upgrade checkstyle to 8.36, correct the hook stages, and stop committing the checkstyle binary to the repository

## [1.0.0] - 2020-09-04

### Added

- Add the `check-jira-issue`, `prepend-jira-issue` and `checkstyle` hooks

[Unreleased]: https://github.com/team-carepay/pre-commit-hooks/compare/v2.0.0...HEAD
[2.0.0]: https://github.com/team-carepay/pre-commit-hooks/compare/v1.3.1...v2.0.0
[1.3.1]: https://github.com/team-carepay/pre-commit-hooks/compare/v1.3.0...v1.3.1
[1.3.0]: https://github.com/team-carepay/pre-commit-hooks/compare/v1.2.0...v1.3.0
[1.2.0]: https://github.com/team-carepay/pre-commit-hooks/compare/v1.1.0...v1.2.0
[1.1.0]: https://github.com/team-carepay/pre-commit-hooks/compare/v1.0.11...v1.1.0
[1.0.11]: https://github.com/team-carepay/pre-commit-hooks/compare/v1.0.0...v1.0.11
[1.0.0]: https://github.com/team-carepay/pre-commit-hooks/releases/tag/v1.0.0
