# openai-ads-api skill

![A Claude Code session answering "which ads are running right now?" through the openai-ads-api skill](assets/social.png)

An Agent Skill for the OpenAI Ads Advertiser API: ad accounts, creative uploads, campaigns,
ad groups, ads, conversion settings, previews, and insights. Uses the open `SKILL.md` format,
so it works in both Codex and Claude Code.

This repository contains exactly one skill; `SKILL.md` is at the root.

## Why this exists

OpenAI shipped an Ads API and, to my genuine surprise, nothing to drive it with: no MCP server, no
agent skill, not even a client library — the quickstart hands you a bearer token and a `curl`
example and wishes you luck. We were already running our own campaigns on that API, so I wrote down
how to do it properly and turned that into a skill instead of keeping it in my own dotfiles. Enjoy!

## Install

With the [skills CLI](https://skills.sh), which installs into Claude Code, Codex, Cursor,
OpenCode and other agents:

```bash
npx skills add wbso-ai/openai-ads-api-skill
```

Add `-a claude-code` or `-a codex` to target one agent. Update later with `npx skills update`.

Without the CLI, clone the repository into your agent's skills folder under the skill's own name:

```bash
git clone https://github.com/wbso-ai/openai-ads-api-skill ~/.claude/skills/openai-ads-api   # Claude Code
git clone https://github.com/wbso-ai/openai-ads-api-skill ~/.agents/skills/openai-ads-api   # Codex and the ~/.agents convention
```

Claude then loads the skill on its own, or on `/openai-ads-api`.

## Structure

```text
SKILL.md
agents/openai.yaml
references/api.md
assets/social.html   # source of the banner above
assets/social.png    # rendered with headless Chromium, also the GitHub social preview
```

## Credentials

The skill never commits, prints, or documents real secret values. It first checks
`OPENAI_ADS_API_KEY`, then `~/.config/openai-ads/token`.

## Source of truth

The skill verifies operations against the official
[OpenAI Ads documentation](https://developers.openai.com/ads/api-quickstart) before use.

## Who builds this

We wrote this skill at [WBSO.ai](https://wbso.ai) for our own campaigns on the OpenAI Ads API, and
published it because plenty of teams drive the same API. WBSO.ai is a Dutch R&D subsidy specialist:
Dutch tech companies work with us to
[WBSO aanvragen](https://wbso.ai/wbso-aanvragen) — claiming the Dutch R&D tax credit for
development hours, AI-assisted and at a fixed price.
