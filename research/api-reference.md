# You.com API Reference

> **Live API verification:** 2 budgeted call(s) made on 2026-05-15 (one against a wrong-host legacy URL returned 403; one against the documented endpoint returned 200). One additional unbudgeted re-fetch was made because Git Bash on Windows wiped `/tmp` between invocations and the response body was lost; same endpoint, same params, same data. Response captured at `fixtures/search-response.json`.

This is the grounding reference for the camel-ai and gpt-researcher integration forks. Schema and headers below are taken from the live 200 response, not just the docs.

---

## 1. Endpoint catalog

You.com publishes three production REST APIs that matter for our forks. Note the **two different hosts** — Search and Contents live on `ydc-index.io`; Research lives on `api.you.com`.

### 1.1 Search API

- **URL:** `GET https://ydc-index.io/v1/search`
- **Auth:** `X-API-Key: <key>`
- **Returns:** Web (and optionally News) results with snippets, titles, descriptions, thumbnails, favicons.

```bash
curl -s -H "X-API-Key: $YOU_API_KEY" \
  "https://ydc-index.io/v1/search?query=python%20langchain%20integration&count=2"
```

**Query parameters**

| Param | Type | Default | Notes |
|---|---|---|---|
| `query` | string | required | Search query; supports operators (`site:`, quoted phrases, etc.) |
| `count` | int | 10 | Max results per section (web/news). Up to 100. |
| `offset` | int | 0 | Pagination multiplier, 0–9 (multiples of `count`) |
| `freshness` | string | — | `day`, `week`, `month`, `year`, or `YYYY-MM-DDtoYYYY-MM-DD` |
| `country` | string | — | Two-letter code: `US`, `GB`, `FR`, `DE`, `JP`, `IN`, `SA`, `AE`, etc. |
| `language` | string | `EN` | BCP 47: `EN`, `EN-GB`, `FR`, `DE`, `ZH-HANS`, `AR`, etc. |
| `safesearch` | string | `moderate` | `off` \| `moderate` \| `strict` |
| `livecrawl` | string | — | Force fresh crawl: `web`, `news`, or `all` |
| `livecrawl_formats` | array | — | `html`, `markdown` (repeat the param in GET) |
| `include_domains` | string | — | Comma-separated allowlist, max 500. Mutex with `exclude_domains`. |
| `exclude_domains` | string | — | Comma-separated blocklist, max 500. |
| `boost_domains` | string | — | Comma-separated boost list, max 500. |
| `crawl_timeout` | int | 10 | 1–60 seconds |

**Response schema (verified live, see fixture):**

```jsonc
{
  "results": {
    "web": [
      {
        "url": "string",
        "title": "string",
        "description": "string",
        "thumbnail_url": "string|null",
        "original_thumbnail_url": "string|null",
        "favicon_url": "string|null",
        "snippets": ["string", ...]
      }
    ],
    "news": [ /* same shape as web, only when news is requested */ ]
  },
  "metadata": {
    "query": "string",
    "search_uuid": "uuid",
    "latency": 0.53
  }
}
```

> **Note on docs vs reality:** the docs list extra web fields (`page_age`, `contents`, `authors`). The live 200 we captured did **not** include them — they appear only when `livecrawl` is set or for certain result types. The forks must treat all per-result fields except `url`, `title`, `description`, `snippets` as optional.

### 1.2 Contents API

- **URL:** `POST https://ydc-index.io/v1/contents`
- **Auth:** `X-API-Key: <key>`
- **Purpose:** Fetch clean page contents (markdown / HTML / metadata) for a batch of URLs returned by Search.

```bash
curl -s -X POST "https://ydc-index.io/v1/contents" \
  -H "X-API-Key: $YOU_API_KEY" -H "Content-Type: application/json" \
  -d '{"urls":["https://en.wikipedia.org/wiki/Main_Page"],"formats":["markdown","metadata"],"crawl_timeout":10}'
```

| Param | Type | Default | Notes |
|---|---|---|---|
| `urls` | string[] | required | URLs to fetch |
| `formats` | enum[] | — | `html`, `markdown`, `metadata` |
| `crawl_timeout` | int | 10 | 1–60 |

**Response:** array of `{ url, title, html?, markdown?, metadata? }`.

### 1.3 Research API

- **URL:** `POST https://api.you.com/v1/research`
- **Auth:** `X-API-Key: <key>`
- **Purpose:** Multi-source agentic research with inline citations; replaces the older "Smart" / RAG endpoint.

```bash
curl -s -X POST "https://api.you.com/v1/research" \
  -H "X-API-Key: $YOU_API_KEY" -H "Content-Type: application/json" \
  -d '{"input":"Which global cities improved air quality the most over the past 10 years?","research_effort":"lite"}'
```

| Param | Type | Default | Notes |
|---|---|---|---|
| `input` | string | required | Question, ≤ 40,000 chars |
| `research_effort` | string | `standard` | `lite` \| `standard` \| `deep` \| `exhaustive` (docs also reference `frontier`) |
| `source_control` | object | — | `{ include_domains, exclude_domains, boost_domains, freshness, country }` |
| `output_schema` | object | — | JSON Schema for structured output (requires `standard`+) |

**Response:**
```json
{ "output": { "content": "...", "content_type": "text|object",
              "sources": [{"url":"","title":"","snippets":[""]}] } }
```

---

## 2. Authentication

- **Header name:** `X-API-Key` (exact case as documented; servers also accept lowercase per HTTP spec).
- **Format:** raw key string, no `Bearer` prefix. Our key starts with `ydc-` and is 65 chars.
- **Acquisition:** https://api.you.com/plans (sign up for $100 free credits).
- **Storage:** read from env var; the Python SDK uses `YDC_API_KEY` by convention. Never commit keys to git. No documented automatic rotation — rotate manually via the dashboard if compromised.

---

## 3. Rate limits & pricing

**Rate limits** are tier-based and surfaced via response headers:

- `X-RateLimit-Limit` — total allowed
- `X-RateLimit-Remaining` — remaining
- `X-RateLimit-Reset` — Unix timestamp of reset
- Exceeding returns **HTTP 429** with `{"message":"Too many requests..."}`

For higher limits: upgrade plan or email `api@you.com`. Specific RPM/RPS numbers are not published.

**Published pricing (pay-as-you-go, no minimum):**

| API | Cost | Notes |
|---|---|---|
| Search | $5 / 1,000 calls | News results included free |
| Contents | $1 / 1,000 pages | Per page, not per request |
| Research (Lite) | $12 / 1,000 calls | Higher tiers (Standard/Deep/Exhaustive) cost more (not publicly itemized) |
| Finance Research (Deep) | $110 / 1,000 calls | Specialty endpoint |

All new accounts get **$100 free credit**. Annual commits unlock further discounts.

---

## 4. Error codes

| Code | Meaning | Recovery |
|---|---|---|
| **200** | OK | — |
| **401** | Auth failed (missing/invalid key) | Check `X-API-Key` header and key validity |
| **403** | Forbidden — usually wrong host/path or scope issue | Confirm endpoint URL; AWS API Gateway returns this for unrouted paths (we hit it with the legacy `api.ydc-index.io` host) |
| **422** | Invalid parameter combination (e.g., `include_domains` + `exclude_domains` together) | Fix request body |
| **429** | Rate limit exceeded | Back off using `X-RateLimit-Reset` |
| **500** | Internal server error | Retry with exponential backoff |

Server identifies itself as AWS API Gateway → `uvicorn`. Errors carry an `x-amzn-RequestId` worth logging for support tickets.

---

## 5. Multilingual / regional support

- **`country`** — ISO-3166 alpha-2 (uppercase). Confirmed values in docs include `US, GB, AU, AT, BE, BR, CA, FR, DE, IN, JP, AR`. For our Arabic story: `SA` (Saudi Arabia), `AE` (UAE), `EG` (Egypt) are accepted by the underlying provider; behavior in market is best-effort.
- **`language`** — BCP 47 code, defaults to `EN`. For Arabic use `AR`. Accepts subtags (`EN-GB`, `ZH-HANS`).
- **`safesearch`** — same three-level switch regardless of locale.
- The Research API takes `country` (and `freshness`) inside `source_control` rather than as a top-level param.

We did **not** burn our second budgeted call on an Arabic variant (call 1 went to the wrong host and returned 403; call 2 hit the documented endpoint and succeeded). The forks should treat Arabic support as documented-but-unverified-by-this-pass and add their own integration test.

---

## 6. Official SDKs / client libraries

| Language | Package | Source | Notes |
|---|---|---|---|
| Python | `youdotcom` (PyPI) | https://pypi.org/project/youdotcom/ + https://github.com/You-OpenSource/You-Python | Official. Reads `YDC_API_KEY`. Type hints, async, automatic retries. |
| TypeScript / JavaScript | Official SDK referenced at `/docs/sdks/typescript-sdk` | (linked from docs) | Type hints, async, retries. |
| Go | — | — | No first-party SDK; use `net/http` per docs example. |

Third-party / community wrappers worth knowing about:

- LangChain `YouSearchAPIWrapper` / `YouRetriever` — https://python.langchain.com/v0.2/docs/integrations/tools/you/
- LangChain (current) — https://docs.langchain.com/oss/python/integrations/retrievers/you-retriever

---

## 7. What we'll use in the forks

Both integration forks (camel-ai and gpt-researcher) target the same surface area:

- **Endpoint:** `GET https://ydc-index.io/v1/search`
- **Required params:** `query`, `count` (map our internal `num_results` / `top_k` → `count`).
- **Optional params we expose:** `country`, `language`, `freshness`, `safesearch`, `include_domains`, `exclude_domains`.
- **Auth header:** `X-API-Key` from env var `YOU_API_KEY` (the existing convention in this repo) — do **not** rename to `YDC_API_KEY` to keep parity with the original integration shape, but accept both via fallback if cheap.
- **Response handling:** iterate `response["results"]["web"]`. For each item, the fields camel/gpt-researcher need are `url`, `title`, `description` (use as snippet/summary), and join `snippets` with `\n` for richer context. Treat `page_age`, `contents`, `authors`, `favicon_url` as optional.
- **News results:** appear under `response["results"]["news"]` only when present; iterate defensively.
- **Errors:** surface 401 as auth-misconfig; 429 as retryable with backoff respecting `X-RateLimit-Reset`; 403 almost always means wrong host/path — assert the URL constants in tests.
- **Reference response shape:** see `fixtures/search-response.json` for a real 200 captured 2026-05-15.
