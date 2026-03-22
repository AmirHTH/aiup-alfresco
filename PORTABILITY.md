# Portable Agent Usage

This repository is packaged as a Claude Code plugin, but the command logic is not Claude-only.

The portable source of truth is:

- [AGENTS.md](./AGENTS.md) for repo-wide rules
- `commands/*.md` for AIUP command behavior
- `skills/*/SKILL.md` for validations or helper workflows referenced by commands
- `agents/*.md` for agent-specific guidance referenced by hooks or commands

Claude adds a slash-command UI on top of those files. Other agents can use the same command content by rendering it into a normal prompt.

## Quick Start

List the available commands:

```bash
./scripts/aiup-command.sh list
```

Render a prompt for Codex:

```bash
./scripts/aiup-command.sh render --agent codex requirements \
  "We need to manage contracts with approval dates"
```

Render a prompt for OpenClaw:

```bash
./scripts/aiup-command.sh render --agent openclaw scaffold
```

Use the output as the prompt you send to the target agent.

## What The Renderer Does

- points the target agent at `AGENTS.md`
- points the target agent at the requested `commands/<name>.md`
- strips Claude-only front matter from the command file
- explains how to handle `skills/` and `agents/` references manually
- passes through any user arguments

## Typical Workflow Outside Claude

1. Run `./scripts/aiup-command.sh render --agent codex requirements "..."`
2. Send the rendered prompt to the agent.
3. After `REQUIREMENTS.md` exists, run `./scripts/aiup-command.sh render --agent codex scaffold`
4. Continue with `content-model`, `behaviours`, `web-scripts`, `events`, `docker-compose`, and `test`

## Claude-Specific Pieces

These remain Claude-specific and are not interpreted automatically by other agents:

- slash-command invocation such as `/scaffold`
- plugin installation via `claude plugin install`
- hook automation in `hooks/hooks.json`
- automatic skill dispatch

Outside Claude, the renderer replaces those features with an explicit prompt you can feed to another agent.
