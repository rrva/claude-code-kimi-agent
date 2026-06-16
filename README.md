# Kimi Code Agent for Claude Code

Delegate coding tasks from **Claude Code** to the **[Kimi Code CLI](https://github.com/MoonshotAI/kimi-code)** — run Kimi as a plugin subagent without patching Claude Code. Ask Claude to spawn a Kimi agent to implement, review, explore, or debug, and Kimi's result (plus a resume command) comes back into your session.

Built entirely on official surfaces — no host-file edits, no forks:

- Claude Code plugins, skills, and subagents
- Kimi Code CLI non-interactive prompt mode: `kimi -p "<task>" --output-format stream-json`
- Kimi resume flags: `--continue`, `--session`

## How it works

```
Claude Code
  └─ skill: kimi   ── or ──   subagent: kimi-code-agent:kimi-code
       └─ Bash → bin/kimi-agent-run
            └─ kimi -p "<task>" --output-format stream-json
                 └─ parsed → Kimi's response + session id + resume command
```

The bundled `bin/kimi-agent-run` helper spawns Kimi in headless prompt mode, parses its streaming `stream-json` output, and returns a concise summary. Claude never drives Kimi interactively — it delegates a task and gets a result back.

## Requirements

- **Kimi Code CLI** on `PATH`, authenticated:
  ```sh
  kimi --version
  kimi login
  ```
- **Claude Code** with plugin support
- **Node.js ≥ 18** (the helper is a small, dependency-free Node script)

## Install

This repository is a single-plugin Claude Code marketplace. Add it straight from
GitHub and install the plugin — no clone needed:

```sh
claude plugin marketplace add rrva/claude-code-kimi-agent
claude plugin install kimi-code-agent@kimi-code
```

### Develop locally

Clone it and load it for a single session without installing:

```sh
git clone https://github.com/rrva/claude-code-kimi-agent
claude --plugin-dir ./claude-code-kimi-agent
```

## Usage

Once installed, ask Claude in natural language:

```text
Spawn a kimi agent to review the current diff.
```
```text
Use the kimi skill to implement the smallest safe fix for this failing test.
```

Or invoke the subagent explicitly:

```text
Spawn the kimi-code-agent:kimi-code subagent to explore this repo and summarize its architecture.
```

The helper prints a session id and a resume command after each run, so you can continue where Kimi left off:

```text
Continue the previous kimi session and add tests for the change.
```

## Watch a run live

`kimi -p` is non-interactive, so by default you only see the final summary. Use `--tee <path>` to mirror Kimi's live `stream-json` to a file as it runs, then `tail -f` that file in another terminal to watch tool calls and output in real time. The summary returned to Claude Code is unchanged — the tee is a side channel for you.

```sh
# terminal A — run it (or let Claude run it)
bin/kimi-agent-run --tee /tmp/kimi.jsonl <<'KIMI_TASK'
Explore this repository and summarize its architecture.
KIMI_TASK
```
```sh
# terminal B — watch
tail -f /tmp/kimi.jsonl
```

`--live` is a shorthand that tees to a temp file and prints the `tail -f` command.

## Helper reference

`bin/kimi-agent-run` runs Kimi's official non-interactive prompt mode. The prompt comes from stdin or `--prompt`.

| Option | Description |
| --- | --- |
| `--prompt <text>` | Prompt text. If omitted, stdin is used. |
| `--continue` | Continue the most recent Kimi session for the cwd. |
| `--session <id>` | Resume a specific Kimi session id. |
| `--model <model>` | Kimi model alias for this invocation. |
| `--worktree[=<name>]` | Ask Kimi to create a git worktree for a new session. |
| `--cwd <path>` | Working directory for the Kimi process. |
| `--tee <path>` | Mirror live `stream-json` to `<path>` (watch with `tail -f`). |
| `--live` | Like `--tee` but to a temp file; prints the tail command. |
| `--timeout-seconds <n>` | Timeout before terminating Kimi. Default: 1800. |
| `--kimi-bin <path>` | Kimi executable. Default: `kimi`. |
| `--dry-run` | Print the resolved Kimi command without running it. |

Smoke test (no Kimi call):

```sh
bin/kimi-agent-run --dry-run <<'KIMI_TASK'
Summarize this repository.
KIMI_TASK
```

## Notes & limitations

- **Permissions:** Kimi prompt mode runs with auto permission — every file, shell, and network action is auto-approved with no prompt. There is no higher "yolo" level in prompt mode, and this holds regardless of the parent Claude Code session's permission mode.
- **`--worktree`** requires a Kimi CLI version that supports `-w/--worktree` in prompt mode.
- The helper depends only on Node's standard library — nothing to `npm install`.

## License

MIT
