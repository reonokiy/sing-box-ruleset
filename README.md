# sing-box-ruleset

Self-maintained [sing-box](https://sing-box.sagernet.org/) rule sets.

Source rules live in [`source/`](source/) as human-editable
[source-format](https://sing-box.sagernet.org/configuration/rule-set/source-format/)
JSON. GitHub Actions compiles them to binary `.srs` and publishes both
formats to the orphan **`rule-set`** branch, so consumption URLs are stable:

```
https://raw.githubusercontent.com/reonokiy/sing-box-ruleset/rule-set/<name>.srs
https://raw.githubusercontent.com/reonokiy/sing-box-ruleset/rule-set/<name>.json
```

## Rule sets

| Name | What it matches |
|---|---|
| `geosite-openai` | OpenAI / ChatGPT / Sora |
| `geosite-anthropic` | Anthropic / Claude |
| `geosite-google-gemini` | Google Gemini / AI Studio / DeepMind |
| `geosite-microsoft` | Microsoft / Office / Azure / Xbox |
| `geosite-apple` | Apple / iCloud / App Store |
| `geosite-category-public-tracker` | Public BitTorrent trackers |
| `geosite-category-ads-all` | Ad / tracking / analytics domains |
| `geosite-bilibili` | Bilibili (mainland: site, API, CDN, games) |
| `geosite-cn-common` | Common mainland China sites (Baidu/Ali/Tencent/ByteDance/…) |

These are **hand-curated**, not generated from upstream. They are starter
lists meant to be grown over time — add domains to the relevant
`source/*.json` and push; CI rebuilds the `rule-set` branch automatically.

`geosite-bilibili` and `geosite-cn-common` deliberately exclude
overseas-facing variants (e.g. `bilibili.tv`) since they are consumed for
**China-direct** routing.

## Mirrored from upstream

`geoip-cn` and `geosite-geolocation-!cn` are too large to hand-maintain
(China IP CIDRs / "every non-China domain"). CI mirrors them as-is from
[SagerNet](https://github.com/SagerNet) into the `rule-set` branch (weekly +
on every build), so configs only ever reference this repo — the data is
unchanged upstream data, just served from here.

## Source format

```json
{
  "version": 1,
  "rules": [
    { "domain_suffix": ["example.com"], "domain": ["exact.example.org"] }
  ]
}
```

Multiple objects in `rules` are OR'd. Use `domain` for exact matches (e.g.
a single Google subdomain) and `domain_suffix` for whole zones.

## Build locally

```sh
sing-box rule-set compile --output geosite-openai.srs source/geosite-openai.json
```
