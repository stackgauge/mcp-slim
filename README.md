# mcp-slim

> Audit your Model Context Protocol (MCP) server configs for **security risks** and **token bloat** — with zero runtime dependencies.

`mcp-slim` is a developer-first CLI that inspects the MCP servers registered with Claude Desktop, Cursor, and Windsurf. It estimates the token footprint each server adds to your context, flags risky permissions (shell execution, broad filesystem access, unpinned packages, plaintext secrets, insecure transports), and can emit a minified, lossless config to trim overhead.

## Why mcp-slim?

Every MCP server you register carries two hidden costs:

- **Token bloat.** Each server's tool schemas are injected into your context on _every_ request. A few chatty servers can quietly burn thousands of tokens — raising cost and crowding out the task at hand.
- **Security exposure.** MCP servers run real commands on your machine. A config can silently grant shell execution, full-filesystem access, plaintext secrets, or unpinned packages that auto-update to whatever a maintainer ships next.

These configs are buried JSON files most people never open. `mcp-slim` makes them legible in a single command.

## Features

- **Config auto-discovery** — finds standard config files across macOS, Windows, and Linux, or point it at your own with `--config`.
- **Security scorecard** — an at-a-glance **A–F grade** with a one-line verdict and a shareable README **badge**.
- **Token audit** — estimates tokens per server (~4 chars/token) and ranks the worst offenders.
- **Security linter** — heuristic checks for shell exec, broad filesystem scopes, unpinned `npx`/`uvx` packages, plaintext secrets, and `http://` endpoints.
- **Lossless compression** — strips insignificant JSON whitespace and collapses whitespace in `description` fields, reporting token savings.
- **CI-ready** — a tunable `--fail-on` gate and a drop-in **GitHub Action** to block risky configs in pull requests.
- **Zero runtime dependencies** — the CLI runs on native Node APIs (`fs`, `path`, `os`) only.

## Use cases

**Quick personal health check** (no install needed):

```bash
npx @stackgauge/mcp-slim audit
```

Auto-finds your Claude / Cursor / Windsurf configs and prints a per-server token table plus ranked security findings.

**Review a shared team config before rollout:**

```bash
npx @stackgauge/mcp-slim audit --config ./team-mcp.json
```

Catches broad filesystem grants or `bash -c` servers before they reach everyone's machine.

**Gate CI / pre-commit on config hygiene** — `audit` exits with code `2` on any HIGH finding:

```bash
npx @stackgauge/mcp-slim audit --config ./mcp.json || exit 1
```

Add `--json` to feed dashboards or custom policy checks.

**Trim context cost** with a lossless, minified config:

```bash
npx @stackgauge/mcp-slim compress
```

## Requirements

- Node.js >= 18

## Usage

No install needed:

```bash
npx @stackgauge/mcp-slim audit
```

Or install globally:

```bash
npm install -g @stackgauge/mcp-slim
mcp-slim audit
```

### Commands

```
mcp-slim audit                 Scan configs and report token usage + security risks
mcp-slim compress              Emit a minified, lossless config and report savings
mcp-slim trends                Report token footprint over time from recorded history
```

### Options

```
--config <path>   Use a specific config file (skips auto-discovery)
--period <p>      Trend bucket: hourly|daily|weekly|monthly|quarterly|yearly (default daily)
--history <path>  History file (default ~/.mcp-slim/history.jsonl, or $MCP_SLIM_HISTORY)
--no-history      Do not record a snapshot when running audit
--badge           audit: print a shields.io security-grade badge for your README
--fail-on <sev>   audit: exit non-zero on a finding at/above none|low|medium|high (default high)
--json            Output machine-readable JSON
-h, --help        Show help
-v, --version     Show version
```

### Examples

```bash
# Audit every config the tool can find
npx @stackgauge/mcp-slim audit

# Audit a specific file
npx @stackgauge/mcp-slim audit --config ./claude_desktop_config.json

# Get JSON for scripting / CI
npx @stackgauge/mcp-slim audit --json

# See how your footprint trends over time (weekly buckets)
npx @stackgauge/mcp-slim trends --period weekly

# See how much you could trim
npx @stackgauge/mcp-slim compress
```

### Exit codes

| Code | Meaning                                                      |
| ---- | ------------------------------------------------------------ |
| 0    | Success, no findings at or above the `--fail-on` threshold   |
| 1    | Usage error or no config found                               |
| 2    | `audit` found a finding at or above `--fail-on` (default high)|

Tune the gate with `--fail-on none|low|medium|high`:

```bash
# Default: fail only on HIGH findings
npx @stackgauge/mcp-slim audit

# Stricter: fail on medium-and-up
npx @stackgauge/mcp-slim audit --fail-on medium

# Report only, never fail
npx @stackgauge/mcp-slim audit --fail-on none
```

## Use as a GitHub Action

Gate your repo's MCP config in CI with the bundled action:

```yaml
# .github/workflows/mcp-audit.yml
name: MCP audit
on: [push, pull_request]
jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: stackgauge/mcp-slim@main
        with:
          config: .mcp/config.json   # optional; omit for auto-discovery
          fail-on: high              # none | low | medium | high
```

**Inputs:** `config` (path to audit), `fail-on` (severity gate), `version` (dist-tag/version of `@stackgauge/mcp-slim`, default `latest`), `args` (extra flags). Pin to a release tag or commit SHA instead of `@main` for reproducible builds.

**Outputs:** `grade` (A–F) and `score` (0–100) — usable in later steps. The action also writes a security summary to the **PR's Checks tab** (via the job step summary):

```yaml
      - uses: stackgauge/mcp-slim@main
        id: mcp
        with:
          fail-on: medium
      - run: echo "MCP security grade is ${{ steps.mcp.outputs.grade }}"
```

## Security scorecard & badge

Every `audit` prints an at-a-glance **security grade** for your config:

```
  [  F  ]  Security grade  0/100
  3 high, 2 medium: broad filesystem access, shell execution, unpinned package
```

The grade (A–F) is scored from the security findings — a clean config earns `A 100/100`; each risk deducts points (high −30, medium −12, low −4).

Add `--badge` to also get a ready-to-paste **shields.io badge** for your README or a social post:

```bash
npx @stackgauge/mcp-slim audit --badge
```

```
![MCP security](https://img.shields.io/badge/MCP%20security-A%20(100%2F100)-brightgreen)
```

Which renders as a badge you can show off:

![MCP security A](https://img.shields.io/badge/MCP%20security-A%20(100%2F100)-brightgreen)

## Discovered config locations

| Product        | macOS                                                                 | Windows                                             | Linux                                            |
| -------------- | --------------------------------------------------------------------- | --------------------------------------------------- | ------------------------------------------------ |
| Claude Desktop | `~/Library/Application Support/Claude/claude_desktop_config.json`     | `%APPDATA%\Claude\claude_desktop_config.json`       | `~/.config/Claude/claude_desktop_config.json`    |
| Cursor         | `~/.cursor/mcp.json`                                                   | `~/.cursor/mcp.json`                                | `~/.cursor/mcp.json`                             |
| Windsurf       | `~/.codeium/windsurf/mcp_config.json`                                 | `~/.codeium/windsurf/mcp_config.json`               | `~/.codeium/windsurf/mcp_config.json`            |

## How token estimation works

v1.0 measures the **static config** — the serialized JSON for each server definition — using the common ~4-characters-per-token heuristic. It does **not** launch servers to introspect their live tool schemas over JSON-RPC (planned for a later release), so treat the numbers as a lower bound and a useful proxy for config bloat.

## Tracking token footprint over time

`mcp-slim` keeps a small local history of your config's token footprint so you can see how it trends. **Every `audit` run records one timestamped snapshot** (per config file) to a JSONL file at `~/.mcp-slim/history.jsonl` by default (override with `--history <path>` or the `$MCP_SLIM_HISTORY` env var; skip recording with `--no-history`).

The `trends` command then aggregates that history into period buckets:

```bash
npx @stackgauge/mcp-slim trends --period daily
```

```
Token footprint trend (daily)
  PERIOD      RUNS    AVG       MIN       MAX       LAST
  2026-11-15  3       1,204     1,180     1,204     1,204
  2026-11-16  5       1,560     1,204     1,980     1,980
```

Periods: `hourly`, `daily`, `weekly` (ISO week), `monthly`, `quarterly`, `yearly`. Add `--json` for machine-readable output. Buckets use your **local** time zone.

Because the footprint only changes when your config changes, a dense time-series comes from **sampling regularly** — schedule `audit` to build history, e.g. hourly via cron:

```bash
0 * * * * npx @stackgauge/mcp-slim audit >/dev/null 2>&1
```

> **What this measures:** the *static config footprint* over time (the same estimate `audit` reports), **not** live per-request token consumption from your Claude/Cursor/Windsurf sessions — those clients don't expose a usage log `mcp-slim` can read. It's a lower-bound proxy for how your MCP context cost evolves as you add, remove, or change servers.

## Development

```bash
npm install
npm run build     # tsc -> dist/
npm run lint
npm test
```

## Author

Created and maintained by **Sandip Gami** ([@next-dna](https://github.com/next-dna)).

Contributions are welcome — open an issue or PR at
[stackgauge/mcp-slim](https://github.com/stackgauge/mcp-slim).

## License

MIT © Sandip Gami
