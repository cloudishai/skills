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

The skill is a copy of **https://cloudish.ai/skill.md** and the docs it links
to (credits, instances, replicas, container isolation, changelog), bundled so
the agent reads them locally. It never fetches instructions at runtime; new
versions ship as new releases of this plugin.

## What it runs and sends

The plugin contains instructions only: no MCP server, hooks, scripts, or
binaries. When you ask it to deploy, the agent uses `curl` and `tar` to call
the Cloudish API at `https://cloudish.ai`, and to check that your deployed app
answers at its own URL (`https://<name>.cloudish.app/`, or your own domain):

- **Sends** a `.tar.gz` of the project folder (excluding `.git` and `.env*`) to
  Cloudish to be built and hosted, plus the project name, port, and any env
  vars or secrets you ask it to set.
- **Creates** a Cloudish API key with an unauthenticated call when there isn't
  one, and saves it as `CLOUDISH_API_KEY` in a gitignored `.env` in your
  project. The key is used only to authenticate API calls to `cloudish.ai`, and
  is never printed to chat.
- **Reads** that `CLOUDISH_API_KEY` back from `.env` on later runs, so it keeps
  using the same Cloudish account. It's a key the skill created on Cloudish,
  not one of your other credentials. A sub-agent can get its own key, minted
  and funded by the parent agent's key.
- **Reports** the agent product and model (for example `claude-code`,
  `claude-opus-5-5`) when it creates a key, if known, plus `skills-repo`
  as the source. All three are optional.

Adding credits happens outside Claude: the agent gives you a one-time
`www.cloudish.ai` link, and any payment is made on that page in your browser.
If your app signs its users in with Cloudish, that sign-in runs between your
deployed app and `www.cloudish.ai`, not through the agent.

The bundled docs are generated from the ones served at cloudish.ai, with links
between them pointing at the local files.

Your deployed code and data are handled under Cloudish's
[privacy policy](https://www.cloudish.ai/privacy) and
[terms](https://www.cloudish.ai/terms).

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
skills/cloudish/SKILL.md          the skill (copy of cloudish.ai/skill.md)
skills/cloudish/*.md              the docs it links to, bundled
.claude-plugin/                   Claude Code plugin + marketplace manifests
plugin.json                       OpenAI / Agent Plugins manifest
assets/cloudish.png               icon
```

## License

[MIT](LICENSE)
