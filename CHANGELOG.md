# Changelog

All notable changes to this project are documented here. This file is
maintained automatically by [release-please](https://github.com/googleapis/release-please)
from [Conventional Commits](https://www.conventionalcommits.org/); do not edit
it by hand.

## [1.3.1](https://github.com/stackgauge/mcp-slim/compare/mcp-slim-v1.3.0...mcp-slim-v1.3.1) (2026-11-24)


### Documentation

* note how to verify the installed version ([#17](https://github.com/stackgauge/mcp-slim/issues/17)) ([a01305d](https://github.com/stackgauge/mcp-slim/commit/a01305d14fd4a5253f50f1ff5aa4cc24c7a71ead))

## [1.3.0](https://github.com/stackgauge/mcp-slim/compare/mcp-slim-v1.2.0...mcp-slim-v1.3.0) (2026-11-19)


### Features

* add --fail-on severity gate and a GitHub Action ([#14](https://github.com/stackgauge/mcp-slim/issues/14)) ([8063897](https://github.com/stackgauge/mcp-slim/commit/806389749d329bd49203b773abb8b689387f6a58))

## [1.2.0](https://github.com/stackgauge/mcp-slim/compare/mcp-slim-v1.1.0...mcp-slim-v1.2.0) (2026-11-18)


### Features

* add security scorecard (A–F grade) and shareable badge ([#12](https://github.com/stackgauge/mcp-slim/issues/12)) ([3fd42c1](https://github.com/stackgauge/mcp-slim/commit/3fd42c1c35829f19ac7541fca75865ccefbdd235))

## [1.1.0](https://github.com/stackgauge/mcp-slim/compare/mcp-slim-v1.0.2...mcp-slim-v1.1.0) (2026-11-17)


### Features

* track token footprint over time with a trends command ([#9](https://github.com/stackgauge/mcp-slim/issues/9)) ([e6584d4](https://github.com/stackgauge/mcp-slim/commit/e6584d4d015bedc7672c3edd91b18c9dc1fa06f6))

## [1.0.2](https://github.com/stackgauge/mcp-slim/compare/mcp-slim-v1.0.1...mcp-slim-v1.0.2) (2026-11-17)


### Bug Fixes

* make OIDC publish work on Node 20 and add manual dispatch ([#4](https://github.com/stackgauge/mcp-slim/issues/4)) ([424ac69](https://github.com/stackgauge/mcp-slim/commit/424ac693f9f187e4111aef76ed3164b79c665310))

## [1.0.1](https://github.com/stackgauge/mcp-slim/compare/mcp-slim-v1.0.0...mcp-slim-v1.0.1) (2026-11-17)


### Miscellaneous Chores

* add .nvmrc pinning Node 20 ([#2](https://github.com/stackgauge/mcp-slim/issues/2)) ([6b8d034](https://github.com/stackgauge/mcp-slim/commit/6b8d0342afc9d2ea14cb9d811212263eb1092d97))

## 1.0.0 (2026-11-17)

### Features

- Initial release of `@stackgauge/mcp-slim`.
- `audit` command: per-server token-footprint estimation and a security linter
  (shell execution, broad filesystem scopes, unpinned packages, plaintext
  secrets, insecure transports).
- `compress` command: lossless minified config with a token-savings report.
- Cross-platform config auto-discovery for Claude Desktop, Cursor and Windsurf.
- Zero runtime dependencies; requires Node.js >= 18.
