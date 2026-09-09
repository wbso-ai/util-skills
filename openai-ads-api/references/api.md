# OpenAI Ads Advertiser API reference

This is a compact operating reference, not a replacement for the live documentation. Verify the
requested operation against the linked official page before use.

## Official documentation

- [Quickstart](https://developers.openai.com/ads/api-quickstart)
- [Authentication](https://developers.openai.com/ads/api-reference/authentication)
- [Ad Account](https://developers.openai.com/ads/api-reference/ad-account)
- [Campaigns](https://developers.openai.com/ads/api-reference/campaigns)
- [Ad Groups](https://developers.openai.com/ads/api-reference/ad-groups)
- [Ads](https://developers.openai.com/ads/api-reference/ads)
- [Insights](https://developers.openai.com/ads/api-reference/insights)
- [Files](https://developers.openai.com/ads/api-reference/files)
- [Conversion Setup](https://developers.openai.com/ads/api-reference/conversion-setup)
- [Campaign Targeting](https://developers.openai.com/ads/campaign-targeting)
- [Conversion-Optimized Campaigns](https://developers.openai.com/ads/conversion-optimized-campaigns)

## Authentication and account

Base URL: `https://api.ads.openai.com/v1`

Send `Authorization: Bearer $OPENAI_ADS_API_KEY` on every request. Each key is scoped to one ad
account. Use an existing environment variable when provided; otherwise load the local credential
file without echoing it:

```bash
if [[ -z "${OPENAI_ADS_API_KEY:-}" && -r "$HOME/.config/openai-ads/token" ]]; then
  export OPENAI_ADS_API_KEY="$(<"$HOME/.config/openai-ads/token")"
fi
```

The persistent local convention is `~/.config/openai-ads/token`, with its directory accessible
only by the user and the token file set to mode `600`. When initially storing a token, use silent
interactive input so it does not enter shell history:

```zsh
mkdir -p "$HOME/.config/openai-ads"
chmod 700 "$HOME/.config/openai-ads"
read -rs "OPENAI_ADS_API_KEY?OpenAI Ads API key: "
printf '%s' "$OPENAI_ADS_API_KEY" > "$HOME/.config/openai-ads/token"
chmod 600 "$HOME/.config/openai-ads/token"
unset OPENAI_ADS_API_KEY
```

Confirm the stored credential with:

```bash
curl --fail-with-body --silent --show-error \
  -H "Authorization: Bearer $OPENAI_ADS_API_KEY" \
  -H "Accept: application/json" \
  "https://api.ads.openai.com/v1/ad_account"
```

Use the returned account timezone and `currency_code` when interpreting times and micros.

## Core hierarchy and endpoints

| Resource | List | Retrieve | Create | Update | State actions |
| --- | --- | --- | --- | --- | --- |
| Campaign | `GET /campaigns` | `GET /campaigns/{id}` | `POST /campaigns` | `POST /campaigns/{id}` | `POST /campaigns/{id}/{activate\|pause\|archive}` |
| Ad group | `GET /ad_groups?campaign_id=...` | `GET /ad_groups/{id}` | `POST /ad_groups` | `POST /ad_groups/{id}` | `POST /ad_groups/{id}/{activate\|pause\|archive}` |
| Ad | `GET /ads?ad_group_id=...` | `GET /ads/{id}` | `POST /ads` | `POST /ads/{id}` | `POST /ads/{id}/{activate\|pause\|archive}` |

List endpoints use `limit` from 1 to 500, default 20, and cursor parameters `after` or `before`.
Use only one cursor direction at a time.

## Creative upload

- Remote image: `POST /upload` with JSON `{ "image_url": "https://..." }`.
- Local image: `POST /upload` as multipart with `file=@path`.
- Store the returned `file_id`; a `chat_card` ad refers to it.
- Custom-audience files use the separate `POST /uploads` endpoint and dedicated audience rules.

## Minimum create payloads

Campaign:

```json
{
  "name": "Campaign name",
  "status": "paused",
  "budget": {
    "lifetime_spend_limit_micros": 25000000
  }
}
```

- `name`: 3–1000 characters with a non-space character.
- `status`: `active` or `paused` on creation.
- `budget.lifetime_spend_limit_micros`: integer, minimum `1000000`.
- `bidding_type`: `impressions`, `clicks`, or `conversions`; defaults to `impressions`.
- Omitting `start_time` can start delivery immediately when active. Omitting location targeting can
  target all available locations.

Ad group for an impression campaign:

```json
{
  "campaign_id": "cmpn_...",
  "name": "Ad group name",
  "status": "paused",
  "context_hints": ["when this offer is useful"],
  "bidding_config": {
    "billing_event_type": "impression",
    "max_bid_micros": 60000
  }
}
```

- Use `billing_event_type: "impression"` for impression campaigns.
- Use `billing_event_type: "click"` for click and conversion campaigns.
- `max_bid_micros` is an integer and is per billing event. For conversion-optimized campaigns it is
  the CPA bid even though billing occurs on click.
- `context_hints` describe situations or keywords for when the offer is useful.

Chat-card ad:

```json
{
  "ad_group_id": "adgrp_...",
  "name": "Internal ad name",
  "status": "paused",
  "creative": {
    "type": "chat_card",
    "title": "User-visible title",
    "body": "User-visible copy.",
    "target_url": "https://example.com/landing-page",
    "file_id": "file_..."
  }
}
```

- Internal `name`: 3–1000 characters.
- `creative.title`: 3–50 characters.
- `creative.body`: at most 100 characters.
- `chat_card` requires `target_url` and an uploaded `file_id`.
- Returned `review_status` is `in_review`, `rejected`, or `approved`.
- `POST /ads/{ad_id}/preview` returns a preview that expires after 24 hours.

## Updates and state

Updates use `POST` and accept partial top-level fields. If a nested `budget`, `bidding_config`, or
`creative` is included, send that whole nested object. Nullable fields may support explicit `null`;
verify the resource reference before clearing one.

Creation supports `active` and `paused`; updates can also use `archived`. Prefer dedicated action
endpoints for state-only changes. Archive is irreversible.

## Insights

General delivery insights:

- `GET /ad_account/insights`
- `GET /campaigns/{campaign_id}/insights`
- `GET /ad_groups/{ad_group_id}/insights`
- `GET /ads/{ad_id}/insights`

Useful query controls:

- `time_granularity`: `hourly`, `daily`, `monthly`, or `none`; default `daily`.
- `aggregation_level`: `ad_account`, `campaign`, `ad_group`, or `ad`, within endpoint scope.
- `time_ranges[]`: one JSON-encoded `unix_range`, `hour_range`, or `date_range`. Bounds cannot be in
  the future and must be within the past five years.
- `fields[]`: repeated canonical fields such as `campaign.clicks`, `campaign.impressions`, and
  `campaign.spend`.
- `filters[]`: repeated JSON objects combined with AND.
- `sort[]`: repeated JSON objects with `field` and `direction`.
- `segments[]`: at most one of `product`, `country`, or `device` when enabled for the account.
- `limit`: 1–2000, default 20; use `after` with `last_id` while `has_more` is true.

Example campaign-level daily report:

```bash
curl --fail-with-body --silent --show-error --get \
  -H "Authorization: Bearer $OPENAI_ADS_API_KEY" \
  --data-urlencode "time_granularity=daily" \
  --data-urlencode "aggregation_level=campaign" \
  --data-urlencode "fields[]=metadata.readable_time" \
  --data-urlencode "fields[]=campaign.id" \
  --data-urlencode "fields[]=campaign.name" \
  --data-urlencode "fields[]=campaign.impressions" \
  --data-urlencode "fields[]=campaign.clicks" \
  --data-urlencode "fields[]=campaign.spend" \
  --data-urlencode 'time_ranges[]={"type":"date_range","since":"2026-09-01","until":"2026-09-07"}' \
  "https://api.ads.openai.com/v1/ad_account/insights"
```

Attributed conversion totals use `POST /conversions/insights`; read the current insights reference
for its request shape and attribution semantics before use.

## Conversion setup

- `GET /conversions/pixels` lists conversion sources and their Pixel IDs.
- `POST /conversions/pixels` creates a web conversion source.
- `GET /conversions/event_settings` lists conversion definitions.
- `POST /conversions/event_settings` creates a definition and links exactly one source ID.
- Use `event_type: "custom"` with `custom_event_name` only for events already emitted as custom
  Pixel events and not covered by the standard event taxonomy.
- `attribution_window_days` must be `30`.
- List existing pixels and event settings before creating anything to avoid duplicate sources or
  definitions.
