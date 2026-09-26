# Issue tracker: GitHub

Issues and specs for this repo live in the repository's GitHub Issues. Use the `gh` CLI for all ticket operations.

## Conventions

- Create an issue: `gh issue create --title "..." --body "..."`
- Read an issue: `gh issue view <number> --comments`
- List open issues: `gh issue list --state open --json number,title,body,labels --jq '[.[] | {number, title, body, labels: [.labels[].name]}]'`
- Comment on an issue: `gh issue comment <number> --body "..."`
- Apply or remove labels: `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- Close an issue: `gh issue close <number> --comment "..."`

The repository remote is `https://github.com/andylan02/CodexFDE02.git`, so GitHub is the default issue source for engineering skills.

## Pull requests as a triage surface

PRs as a request surface: no.

This repo does not currently treat external pull requests as triage-first feature requests. Engineering skills should prioritize GitHub Issues unless a later workflow explicitly opts into PR-based triage.

## When a skill says "publish to the issue tracker"

Create a GitHub issue in this repo.

## When a skill says "fetch the relevant ticket"

Use `gh issue view <number> --comments` and related issue metadata.

## Domain notes

This repo uses a single-context layout by default. Domain docs are not yet present at the repo root, so the engineering skills should proceed without assuming a bespoke glossary unless one is added later.
