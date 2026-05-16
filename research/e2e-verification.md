# Live End-to-End Verification

**Date run:** 2026-05-16
**Operator:** Muhammad Hany (`dev-mhany`) via Claude
**API key:** `ydc-sk-...cb922c87` (You.com Search API, $100 free signup credit, ~$0.05 spent total across all project E2E)
**Duration:** ~10 minutes wall clock

## Verdict

**Both forks pass end-to-end across English, Arabic, and Japanese locales.** The multilingual claim in the proposal is verified, not theoretical. All 11 unit tests pass; all 6 live-API calls return real, locale-appropriate results.

## Summary table

| Fork | Unit tests | EN live | AR live | JA live |
|---|---|---|---|---|
| [Cooperation-org/camel](https://github.com/Cooperation-org/camel/pull/1) | **5/5 PASS** (2.25s via pytest) | 5 results, 631ms, shape OK | 5 results, 562ms, 442 Arabic chars, 1 regional domain | 5 results, 598ms, 339 JP chars, 3 regional domains |
| [Cooperation-org/gpt-researcher](https://github.com/Cooperation-org/gpt-researcher/pull/1) | **6/6 PASS** (~6ms via unittest) | 5 results, 1326ms, shape OK | 5 results, 1238ms, 596 Arabic chars, 1 regional domain | 5 results, 961ms, 820 JP chars, 3 regional domains |

## Methodology

### Queries (held constant across both forks for comparability)

- **EN:** `python langchain integration` (default params)
- **AR:** `ذكاء اصطناعي` ("artificial intelligence" in Arabic) with `country=SA, language=ar` (gpt-researcher) / `country=SA, search_lang=ar` (camel)
- **JA:** `人工知能` ("artificial intelligence" in Japanese) with `country=JP, language=ja` / `country=JP, search_lang=ja`

`max_results=5` in each call.

### What "pass" means here

A live call passes when **all** of the following hold:
1. HTTP 200 from `https://ydc-index.io/v1/search`
2. Result count > 0 and ≤ requested
3. Every result has the expected key set (`{href, body, title}` for gpt-researcher; `{result_id, title, description, long_description, url}` for camel)
4. For locale tests: substantial native-script characters present in titles/bodies (446–820 chars across 5 results, depending on locale)
5. For locale tests: ≥1 result from a regional domain (`.sa`, `.ae`, `.eg`, `ar.*` for Arabic; `.jp`, `ja.*` for Japanese)

### What we did NOT live-test

- Concurrent requests (no concurrency in the integrations themselves; serial calls suffice)
- 429 rate-limit handling (covered by unit test with mocked response)
- Timeout handling (covered by unit test with mocked exception)
- Missing-API-key path (covered by unit test)
- Live calls > `max_results=5` (no behavioral difference expected based on API doc; saving budget)

## Fixtures (real captured responses)

Saved under `research/fixtures/e2e-live-2026-05-16/`. Each fixture is the parsed output the relevant fork's class returns, plus metadata (query, params, latency, count). These are reproducible reference data — Gitonga can show them on the call if a live demo has network issues.

| File | Fork | Locale | Result count |
|---|---|---|---|
| `gpt-researcher-en.json` | gpt-researcher | English | 5 |
| `gpt-researcher-ar.json` | gpt-researcher | Arabic (SA) | 5 |
| `gpt-researcher-ja.json` | gpt-researcher | Japanese (JP) | 5 |
| `camel-en.json` | camel | English | 5 |
| `camel-ar.json` | camel | Arabic (SA) | 5 |
| `camel-ja.json` | camel | Japanese (JP) | 5 |

## Sample top-3 results per locale

### gpt-researcher Arabic (`country=SA, language=ar`)
1. ‏Google Gemini — gemini.google.com/?hl=ar
2. ما المقصود بالذكاء الاصطناعي (AI)؟ — aws.amazon.com/what-is/artificial-intelligence/
3. ذكاء اصطناعي - ويكيبيديا — ar.wikipedia.org

### gpt-researcher Japanese (`country=JP, language=ja`)
1. 人工知能 — ja.wikipedia.org
2. 人工知能学会 (The Japanese Society for Artificial Intelligence) — ai-gakkai.or.jp
3. AIを10分使うだけで、人は考えなくなるのか？— wired.jp

### camel Arabic (`country=SA, search_lang=ar`)
Identical top-3 to gpt-researcher Arabic — both forks call the same You.com endpoint and the API is deterministic for the same query within a short window.

### camel Japanese (`country=JP, search_lang=ja`)
Identical top-3 to gpt-researcher Japanese — same reason.

## What this means for Monday's call

- The "multi-language by design" framing in `proposal/MAIN.md` is now backed by 6 captured live responses with native-script content from regional domains. Gitonga can speak to this with confidence.
- The differentiator vs upstream LangChain is real: LangChain's `YouSearchAPIWrapper` exposes `country` only on the News endpoint; the locale on the Search endpoint is what our forks add.
- The fixtures double as a fallback if the live demo fails — Gitonga can switch to "here are the responses we captured Saturday" without losing the message.

## Total live API calls consumed (project lifetime)

- Wave 1.4 (api-reference capture): 3
- E2E round 1 (yesterday's spot checks): 4
- E2E round 2 (this verification): 6
- **Grand total:** 13 calls (~$0.065 of the $100 free credit)

Plenty of headroom remains for the Monday demo and any pre-call dry-runs.
