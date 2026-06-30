# Contributing to Remnant

Thank you for your interest in contributing to Remnant! We welcome all contributions. To ensure the project remains sustainable and clear, please follow the guidelines below.

## Pull Request Naming
To keep our history organized and linked to our issue tracker, all Pull Requests must follow this naming convention:

`#<issue number> - <Brief summary>`

*Example: `#42 - Fix GCS upload timeout during network instability`*

## Commit Message Guidelines (Conventional Commits)
We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification. Each commit message must be structured as follows:

`<type>(<scope>): #<issue number> - <description>`

* **Types:** `feat` (new feature), `fix` (bug fix), `docs` (documentation), `style` (formatting), `refactor` (code changes), `test` (adding tests), `chore` (maintenance).
* **Example:** `fix(service): #42 - Increase GCS upload timeout`

## Developer Certificate of Origin (DCO)
By contributing to this project, you agree to the Developer Certificate of Origin (DCO). You must sign off your commits by using the `-s` flag:

```bash
git commit -s -m "fix(service): #42 - Increase GCS upload timeout"
