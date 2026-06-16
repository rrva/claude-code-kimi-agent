# Kimi Code Agent for Claude Code

This Claude Code plugin exposes Kimi Code CLI as a plugin subagent without modifying Claude Code itself.

It uses only official surfaces:

- Claude Code plugin directories, skills, and plugin subagents
- Kimi Code CLI `kimi -p ... --output-format stream-json`
- Kimi Code CLI resume flags such as `--continue` and `--session`

## Requirements

- Kimi Code CLI on `PATH`
- Kimi configured with `kimi login`
- Claude Code with plugin support

Verify Kimi:

```sh
kimi --version
kimi doctor
```

## Install

This repository is both the plugin and a single-plugin marketplace. Install it
into Claude Code from a local clone:

```sh
claude plugin marketplace add /path/to/claude-kimi
claude plugin install kimi-code-agent@kimi-code
```

Or load it for one session only, without installing:

```sh
claude --plugin-dir /path/to/claude-kimi
```

Then ask Claude:

```text
Use the kimi skill to review the current diff.
```

Or ask directly:

```text
Spawn the kimi-code-agent:kimi-code subagent to implement the smallest safe fix for this failing test.
```

## Wrapper

The bundled `bin/kimi-agent-run` helper is added to Claude Code's Bash `PATH` while the plugin is enabled.

Manual smoke test:

```sh
bin/kimi-agent-run --dry-run <<'KIMI_TASK'
Summarize this repository.
KIMI_TASK
```

Actual run:

```sh
bin/kimi-agent-run <<'KIMI_TASK'
Summarize this repository.
KIMI_TASK
```

Continue the most recent Kimi session for the current working directory:

```sh
bin/kimi-agent-run --continue <<'KIMI_TASK'
Continue with the next step.
KIMI_TASK
```
