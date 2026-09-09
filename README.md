# Utility skills

A collection of public, reusable Codex skills for general-purpose workflows. Every skill is
self-contained under `skills/<skill-name>` and can be installed independently.

## Available skills

### `openai-ads-api`

Safely inspect and operate the OpenAI Ads Advertiser API. Covers ad accounts, creative uploads,
campaigns, ad groups, ads, conversion settings, previews, and insights.

Install by asking Codex:

```text
Use $skill-installer to install https://github.com/wbso-ai/util-skills/tree/main/skills/openai-ads-api
```

The skill becomes available as `$openai-ads-api` on the next turn.

## Repository structure

```text
skills/
└── openai-ads-api/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── references/
        └── api.md
```

Each new skill belongs in its own hyphen-case directory and must include a valid `SKILL.md`.
Supporting scripts, references, assets, and agent metadata should live inside that skill directory.
Skills in this repository must remain generally reusable and must not depend on private
company-specific repositories, terminology, credentials, or infrastructure.

## Credentials

Skills must never commit, print, or document real secret values. The `openai-ads-api` skill first
checks `OPENAI_ADS_API_KEY`, then `~/.config/openai-ads/token`. Its documentation includes a safe
local setup command.

## Sources

API-oriented skills verify operations against the relevant current vendor documentation before
use. The `openai-ads-api` skill uses the official
[OpenAI Ads documentation](https://developers.openai.com/ads/api-quickstart) as its source of
truth.
