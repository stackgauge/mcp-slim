# Examples

Two sample MCP configs so you can see `mcp-slim` work in one command — no setup, no real servers needed. They are the same config written two ways: once with common mistakes, once cleaned up.

## Try it

From the repo root (or anywhere, with the paths adjusted):

```bash
# A risky config — grades F and exits non-zero (2)
npx @stackgauge/mcp-slim audit --config examples/risky-config.json

# The cleaned-up version — grades A and exits 0
npx @stackgauge/mcp-slim audit --config examples/clean-config.json
```

## What the contrast shows

| | `risky-config.json` | `clean-config.json` |
| --- | --- | --- |
| Grade | **F** (0/100) | **A** (100/100) |
| Filesystem scope | root `/` (everything) | a single project folder |
| Shell server | `bash -c ...` (runs arbitrary commands) | removed |
| Package pinning | unpinned `npx` (auto-updates to whatever ships next) | pinned to exact versions |
| Transport | `http://` | `https://` |

Run the risky one, read the findings, then diff the two files to see exactly what changed:

```bash
diff examples/risky-config.json examples/clean-config.json
```

That risky→clean move is the whole point of the tool: it turns a buried JSON file into a short, actionable list.

> These are illustrative fixtures, not a config to copy verbatim — point `--config` at your own `claude_desktop_config.json`, `~/.cursor/mcp.json`, or Windsurf config to audit your real setup (or run `audit` with no flags to auto-discover it).
