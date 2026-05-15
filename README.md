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
| `source/cn/geosite-<app>` | One set per mainland app/company (`geosite-baidu`, `geosite-tencent`, `geosite-bytedance`, …) |
| `geosite-cn-common` | CI aggregate of every `source/cn/` set (one tag for configs) |

These are **hand-curated**, not generated from upstream. They are starter
lists meant to be grown over time — add domains to the relevant
`source/**.json` and push; CI rebuilds the `rule-set` branch automatically.

`geosite-bilibili` and the per-app China sets deliberately exclude
overseas-facing variants (e.g. `bilibili.tv`) since they are consumed for
**China-direct** routing.

### Layout

- `source/*.json` — top-level sets, each compiled to `<name>.srs`.
- `source/cn/*.json` — per-app mainland-China sets; each is **also** its
  own `<name>.srs`, **and** all of them are merged (OR'd) by CI into a
  single `geosite-cn-common.srs`. Drop a new `source/cn/geosite-foo.json`
  in and it joins the aggregate automatically — no manifest, the directory
  is the membership.

> The merge is a CI convenience, **not** a sing-box feature: `rule-set
> compile` takes one source file. sing-box's native ways to combine are a
> multi-rule source file or `"rule_set": ["a","b",…]` in config.

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
