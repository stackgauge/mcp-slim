# Contributing to mcp-slim

Thanks for your interest in improving `mcp-slim`! This project uses a
pull-request workflow with automated releases, so a few conventions keep things
smooth.

## Development setup

```bash
git clone https://github.com/stackgauge/mcp-slim.git
cd mcp-slim
npm install
npm run build
npm test
npm run lint
```

## Branching & pull requests

- `main` is protected — **no direct pushes**. All changes land via pull request.
- Branch from `main` using a short prefix: `feat/…`, `fix/…`, `chore/…`, `docs/…`.
- Open a PR. CI (lint + build + tests on Node 18/20/22) must pass before merge.
- Keep PRs focused and small where possible.

## Conventional Commits (required)

Commit messages — and PR titles, since we squash-merge — must follow
[Conventional Commits](https://www.conventionalcommits.org/). This is how
releases and the changelog are generated automatically.

| Type        | When to use it                                   | Release effect        |
| ----------- | ------------------------------------------------ | --------------------- |
| `feat:`     | A new user-facing feature                        | Minor version bump    |
| `fix:`      | A bug fix                                         | Patch version bump    |
| `docs:`     | Documentation only                               | No release            |
| `test:`     | Adding or fixing tests                           | No release            |
| `refactor:` | Code change that neither fixes a bug nor adds a feature | No release     |
| `chore:`    | Tooling, deps, CI, housekeeping                  | No release            |
| `perf:`     | A performance improvement                        | Patch version bump    |

A breaking change is marked with a `!` (e.g. `feat!:`) or a
`BREAKING CHANGE:` footer, which triggers a major bump.

Examples:

```
feat: add --format json to the compress command
fix: resolve Windsurf config path on Windows
docs: clarify token-estimation caveat in the README
```

## How releases work

1. Merge Conventional-Commit PRs into `main`.
2. [release-please](https://github.com/googleapis/release-please) opens and keeps
   a **"Release PR"** that bumps the version and updates `CHANGELOG.md`.
3. When a maintainer merges that Release PR, a git tag and GitHub Release are
   created, and the release workflow publishes to npm via **OIDC Trusted
   Publishing** (no tokens involved).

You never run `npm publish` locally.
