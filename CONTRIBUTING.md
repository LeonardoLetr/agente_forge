# Contributing

## Branches

Create a fresh branch for every change, using English names with a type prefix and kebab-case description:

- `feat/agent-runtime`
- `feat/world-engine`
- `fix/memory-persistence`
- `docs/initial-project-documentation`
- `refactor/event-model`
- `test/dungeon-validation`
- `chore/ci-setup`

Protect `main` as the integration branch; submit changes using Pull Requests. The initial bootstrap commit on `main` is an exception required for an empty repository.

## Commits

Write commit messages in English using [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/):

- `feat: implement agent decision loop`
- `fix: prevent invalid dungeon transitions`
- `docs: add initial dungeon evolution lab documentation`
- `refactor: extract world state reducer`
- `test: cover deterministic dungeon generation`
- `chore: configure continuous integration`

Use the author's own verified Git name and email; never impersonate a contributor or rely on an integration's default author for work intended to be credited to a specific person. The GitHub no-reply address is an option for privacy when linked to the relevant account.

## Pull requests

Describe the purpose, implementation, verification performed, and any remaining limitations. Keep PRs focused on one feature or concern. Avoid merging without review, and avoid rewriting shared history.