<p align="center">
  <img src="assets/cloudish.png" alt="Cloudish" width="160">
</p>

<h1 align="center">Cloudish Skills</h1>

<p align="center">
  Agent Skills for <a href="https://cloudish.ai">Cloudish</a>, the cloud an AI agent deploys a container to.
</p>

Cloudish lets an agent create its own API key with one unauthenticated call,
build any Dockerfile or source folder server-side, and run it as a long-lived
container with an optional persistent volume, paid from prepaid credits the key
can never exceed. No browser, no card, no local Docker.

## Skills

| Skill | What it does |
|---|---|
| [`cloudish`](skills/cloudish/SKILL.md) | Prepares a project, deploys it to Cloudish, and reports the live URL. |

The skill here is intentionally short: it carries the workflow and guardrails,
and tells the agent to read **https://cloudish.ai/skill.md** for the current
API. That file is the canonical, always up-to-date reference.

## Install

**Claude Code**

```
/plugin marketplace add cloudishai/skills
/plugin install cloudish@cloudishai
```

**Any agent that reads `SKILL.md`** (Claude Code, Codex, Cursor, …) — copy
[`skills/cloudish`](skills/cloudish) into your agent's skills directory, or
install the full skill straight from Cloudish:

```bash
mkdir -p ~/.claude/skills/cloudish
curl -s https://cloudish.ai/skill.md -o ~/.claude/skills/cloudish/SKILL.md
```

Then ask your agent: *"Deploy this app to Cloudish."*

## Layout

```
skills/cloudish/SKILL.md          the skill
.claude-plugin/                   Claude Code plugin + marketplace manifests
plugin.json                       OpenAI / Agent Plugins manifest
assets/cloudish.png               icon
```

## License

[MIT](LICENSE)
