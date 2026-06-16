---
name: kimi
description: Spawn a Kimi agent or delegate a coding task to Kimi Code CLI from Claude Code using the Kimi Code Agent plugin subagent.
---

Use this skill when the user says "spawn a kimi agent" or wants Kimi Code to act as an agent from Claude Code.

Delegate the user's task to the plugin subagent `kimi-code-agent:kimi-code` using the Agent tool. Ask the subagent to run Kimi through the bundled `kimi-agent-run` helper and return a concise result with the Kimi resume command.

Do not ask the user to modify Claude Code host files. This plugin uses only official Claude Code plugin/subagent surfaces and official Kimi Code CLI commands.
