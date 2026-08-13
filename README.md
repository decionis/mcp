# Decionis MCP — agent policy tools & execution hooks

**Before AI acts, Decionis decides.** [`@decionis/mcp`](https://www.npmjs.com/package/@decionis/mcp) is a stdio [Model Context Protocol](https://modelcontextprotocol.io) server that lets AI coding agents — Claude Code, Cursor, Codex, Copilot, OpenHands — read a repository's `DECIONIS_POLICY.md` and evaluate candidate actions **before** they commit, deploy, or migrate anything. Locally, with **zero network, zero credentials, and nothing recorded**.

[![npm](https://img.shields.io/npm/v/%40decionis%2Fmcp?color=6D28D9)](https://www.npmjs.com/package/@decionis/mcp)
[![License: Apache-2.0](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)
[![Docs](https://img.shields.io/badge/docs-protocol--mcp-1f6feb)](https://decionis.com/docs/protocol-mcp?utm_source=github&utm_medium=org_readme&utm_campaign=dev_discovery)

It is not a re-implementation: `decionis_evaluate` boots the real `@decionis/protocol` evaluator in-process, publishes your repo policy through the protocol's own schema-validated bundle ingestion, and evaluates through the same path the platform uses. **The verdict an agent sees locally is the verdict the platform would produce.**

## 60-second setup

No install needed — every client below launches the server with `npx -y @decionis/mcp`.

**Claude Code** — one command:

```bash
claude mcp add decionis -- npx -y @decionis/mcp
```

or `.mcp.json` at the repo root:

```json
{
  "mcpServers": {
    "decionis": {
      "command": "npx",
      "args": ["-y", "@decionis/mcp"]
    }
  }
}
```

**Codex** — `~/.codex/config.toml`, or a trusted project's `.codex/config.toml`:

```toml
[mcp_servers.decionis]
command = "npx"
args = ["-y", "@decionis/mcp"]
required = true
```

**Cursor** — use the same server block as Claude Code in `.cursor/mcp.json`.

## Tools

| Tool                    | Purpose                                                                                                                 |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `decionis_read_policy`  | Path, sha256, and compiled rules (or compile errors) of the repo's `DECIONIS_POLICY.md`.                                 |
| `decionis_evaluate`     | Evaluate a candidate action payload against the policy through the real evaluator; returns the verdict + matched rule.   |
| `decionis_verdict_help` | The verdict vocabulary, rules-block grammar, and how an agent should behave on each verdict.                             |

The policy file resolves from the tool's `path` argument, then `$DECIONIS_POLICY_PATH`, then `./DECIONIS_POLICY.md` in the working directory.

## Write your policy

`DECIONIS_POLICY.md` is plain Markdown at your repo root — rules the way your team already thinks:

```markdown
# Decionis Policy

## Rules

### Shell commands
- **Block** destructive commands (`rm -rf`, `DROP TABLE`) without an approved change request.

### Production deploys
- **Escalate** any production deploy outside business hours.
- **Allow** deploys with a green CI run and an approved PR.

### AI-generated changes
- **Restrain** agent-authored changes that touch deploy, infra, or migration paths until a human signs off.
```

Start from a forkable policy on the [Policy Exchange](https://decionis.com/policy-exchange?utm_source=github&utm_medium=org_readme&utm_campaign=dev_discovery) (e.g. `claude-code-destructive-shell-gate`), or see the [annotated example policies](https://github.com/decionis/govern/tree/main/examples) in the govern repo.

## Native execution hooks — binding enforcement

MCP tools are the **discovery and explanation** surface: the model can consult them. The native `PreToolUse` hooks are the **binding** surface: they intercept every supported tool call *outside the model's discretion*. Codex, GitHub Copilot, and Claude Code all normalize into one `AgentToolCall` contract and one `AgentGateEvaluator` interface.

```bash
npm install -g @decionis/mcp   # puts decionis-agent-hook on the PATH
```

Copy the provider template (ships inside the package) to its native repository location:

| Host                                    | Template                        | Native location               |
| --------------------------------------- | ------------------------------- | ----------------------------- |
| Codex                                   | `templates/CodexHooks.json`     | `.codex/hooks.json`           |
| Claude Code                             | `templates/ClaudeSettings.json` | `.claude/settings.json`       |
| GitHub Copilot CLI / cloud agent / VS Code | `templates/CopilotHooks.json`   | `.github/hooks/Decionis.json` |

### Local vs remote evaluation

The hook defaults to **local mode** — it loads `DECIONIS_POLICY.md`, publishes it into the in-process evaluator, and enforces the result with no network access or credentials.

Switch to the enterprise policy graph and signed decision pipeline with:

```bash
DECIONIS_AGENT_GATE_MODE=remote
DECIONIS_AGENT_GATE_URL=https://protocol.decionis.com/v1/protocol/evaluate-decision
DECIONIS_API_KEY=<org-scoped-key>
DECIONIS_ORG_ID=<org-uuid>
```

`DECIONIS_AGENT_GATE_TIMEOUT_MS` optionally changes the remote request timeout (default 4 s). Partial remote configuration is rejected; network errors, invalid hook input, missing policy, and evaluator failures all produce a native **deny**.

An `APPROVE` result adds no permission override — the host's normal sandbox and approval flow still applies. `REJECT`, `REVIEW`, and `ESCALATE` all stop the tool call; the latter two stay blocked until a human resolves them through the policy workflow.

## Machine discovery

- MCP surfaces: `https://decionis.com/.well-known/mcp.json`
- Full API contract: `https://decionis.com/.well-known/openapi.json`

## Source, issues, and related

**Source of truth** for the published npm package lives in the Decionis platform monorepo; this repository is the public home for setup guides, samples, and issue reports. Bugs and feature requests are welcome here — the [issue forms](https://github.com/decionis/.github/tree/master/ISSUE_TEMPLATE) route them to the right package.

- Gating CI/CD pipelines instead of agents? The same gate is a GitHub Action: [`decionis/govern`](https://github.com/decionis/govern) ([Marketplace](https://github.com/marketplace/actions/decionis-action-gate)).
- Try a live policy check in the [Sandbox](https://decionis.com/sandbox?utm_source=github&utm_medium=org_readme&utm_campaign=dev_discovery) or open the [Board](https://board.decionis.com/?utm_source=github&utm_medium=org_readme&utm_campaign=dev_discovery).
- Security issues: **never** open a public issue — see our [security policy](https://github.com/decionis/.github/blob/master/SECURITY.md) / [security@decionis.com](mailto:security@decionis.com).
