# OpenAI Ads API skill

A Codex skill for safely inspecting and operating the OpenAI Ads Advertiser API. It covers ad
accounts, creative uploads, campaigns, ad groups, ads, conversion settings, previews, and insights.

The skill keeps live mutations explicit, defaults new delivery objects to paused, displays budgets
in account currency and micros, and never prints or commits API keys.

## Install with Codex

Ask Codex:

```text
Use $skill-installer to install https://github.com/wbso-ai/openai-ads-api-skill/tree/main/openai-ads-api
```

The skill becomes available as `$openai-ads-api` on the next turn.

## Credential

The skill first checks `OPENAI_ADS_API_KEY`, then `~/.config/openai-ads/token`. To store the key
from the macOS clipboard without putting it in shell history:

```zsh
mkdir -p ~/.config/openai-ads && chmod 700 ~/.config/openai-ads && pbpaste | tr -d '\r\n' > ~/.config/openai-ads/token && chmod 600 ~/.config/openai-ads/token
```

Create an Ads API key in [OpenAI Ads Manager](https://ads.openai.com). Each key is scoped to one ad
account.

## Sources

The skill verifies operations against the current [OpenAI Ads documentation](https://developers.openai.com/ads/api-quickstart)
before use. Its bundled API reference is intentionally compact and should not replace the live
documentation.
