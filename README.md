# openai-ads-api skill

An Agent Skill for the OpenAI Ads Advertiser API: ad accounts, creative uploads, campaigns,
ad groups, ads, conversion settings, previews, and insights. Uses the open `SKILL.md` format,
so it works in both Codex and Claude Code.

This repository contains exactly one skill; `SKILL.md` is at the root.

## Install in Codex

Ask Codex:

```text
Use $skill-installer to install https://github.com/wbso-ai/openai-ads-api-skill
```

The skill becomes available as `$openai-ads-api` on the next turn.

## Install in Claude Code

Clone the repository and symlink it into your personal Claude skills directory:

```bash
git clone --depth 1 https://github.com/wbso-ai/openai-ads-api-skill.git ~/.claude/openai-ads-api-skill
mkdir -p ~/.claude/skills
ln -s ~/.claude/openai-ads-api-skill ~/.claude/skills/openai-ads-api
```

Claude then loads the skill automatically or on `/openai-ads-api`. Update later with:

```bash
git -C ~/.claude/openai-ads-api-skill pull --ff-only
```

## Structure

```text
SKILL.md
agents/openai.yaml
references/api.md
```

## Credentials

The skill never commits, prints, or documents real secret values. It first checks
`OPENAI_ADS_API_KEY`, then `~/.config/openai-ads/token`.

## Source of truth

The skill verifies operations against the official
[OpenAI Ads documentation](https://developers.openai.com/ads/api-quickstart) before use.
