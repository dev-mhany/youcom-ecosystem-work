# LangChain You.com Integration — Benchmark Report

This is the **quality benchmark** that the camel-ai and gpt-researcher forks must match. Every claim below is sourced verbatim from upstream files fetched 2026-05-15.

## Path drift

The original spec referenced `langchain-ai/langchain` at `libs/community/...`. That path **404s on both `master` and `main`**. LangChain has split `langchain-community` into its own repository: `langchain-ai/langchain-community`. The package-internal layout (`libs/community/langchain_community/...`) is preserved.

Canonical sources used for this benchmark:

- Wrapper: `https://raw.githubusercontent.com/langchain-ai/langchain-community/main/libs/community/langchain_community/utilities/you.py`
- Retriever: `https://raw.githubusercontent.com/langchain-ai/langchain-community/main/libs/community/langchain_community/retrievers/you.py`
- Tool: `https://raw.githubusercontent.com/langchain-ai/langchain-community/main/libs/community/langchain_community/tools/you/tool.py`
- Tests: `https://raw.githubusercontent.com/langchain-ai/langchain-community/main/libs/community/tests/unit_tests/utilities/test_you.py`

## 1. `YouSearchAPIWrapper` API surface

Verbatim class signature (Pydantic v2 `BaseModel` from `pydantic`, not `langchain_core.pydantic_v1`):

```python
class YouSearchAPIWrapper(BaseModel):
    ydc_api_key: Optional[str] = None
    endpoint_type: Literal["search", "news", "rag", "snippet"] = "search"
    num_web_results: Optional[int] = None
    safesearch: Optional[Literal["off", "moderate", "strict"]] = None
    country: Optional[str] = None
    search_lang: Optional[str] = None      # News only
    ui_lang: Optional[str] = None          # News only
    spellcheck: Optional[bool] = None      # News only
    k: Optional[int] = None                # post-fetch cap on Documents returned
    n_snippets_per_hit: Optional[int] = None  # search only
    n_hits: Optional[int] = None           # deprecated alias for num_web_results
```

Public methods:

| Method | Signature | Purpose |
| --- | --- | --- |
| `raw_results` | `(query: str, **kwargs) -> Dict` | Sync HTTP GET, returns the raw JSON payload. |
| `results` | `(query: str, **kwargs) -> List[Document]` | Calls `raw_results` then `_parse_results`. |
| `raw_results_async` | `async (query: str, **kwargs) -> Dict` | Same as `raw_results` over `aiohttp`. |
| `results_async` | `async (query: str, **kwargs) -> List[Document]` | Async parsed variant. |

Three Pydantic `model_validator`s run at construction time:
1. `validate_environment` (mode=`before`) — pulls `YDC_API_KEY` from env if `ydc_api_key` was not supplied, via `langchain_core.utils.get_from_dict_or_env`.
2. `warn_if_set_fields_have_no_effect` (mode=`after`) — emits `UserWarning` if News-only fields are set on a non-news endpoint, or if `n_snippets_per_hit` is set on a non-search endpoint.
3. `warn_if_deprecated_endpoints_are_used` — emits `DeprecationWarning` if `endpoint_type == "snippets"` (note: typo in upstream — checks `"snippets"` plural even though the `Literal` accepts `"snippet"` singular; the deprecation branch is effectively dead).

## 2. Endpoints called

- **Base URL constant**: `YOU_API_URL = "https://api.ydc-index.io"`
- **Path**: `f"{YOU_API_URL}/{self.endpoint_type}"` — i.e. `/search`, `/news`, `/rag`, or `/snippet`. Before the request, `endpoint_type == "snippet"` is rewritten in-place to `"search"`.
- **Method**: `GET` (both sync via `requests.get` and async via `aiohttp.ClientSession.get`).
- **Headers**: `{"X-API-Key": self.ydc_api_key or ""}` — note the empty-string fallback (no auth error if key missing; server will 401).
- **Query params**: built by `_generate_params`. For `search`/`snippet`: `query`, `num_web_results`, `safesearch`, `country`. For `news`: `q`, `count`, `safesearch`, `country`, `search_lang`, `ui_lang`, `spellcheck`. `None` values are stripped before sending. Caller `**kwargs` are merged in (and override) the wrapper attributes.

## 3. Response shape returned to caller

`results()` / `results_async()` return `List[langchain_core.documents.Document]`.

For `search`/`snippet` endpoints, each `Document` is one snippet:

```python
Document(
    page_content=snippet,                # str
    metadata={
        "url": hit["url"],
        "thumbnail_url": hit["thumbnail_url"],
        "title": hit["title"],
        "description": hit["description"],
    },
)
```

For `news`, the `description` field becomes `page_content` and the **entire raw result dict** is dumped into `metadata`:

```python
Document(page_content=result["description"], metadata=result)
```

`k` caps the total `Document` count returned (applied post-flatten for search, post-slice for news). `n_snippets_per_hit` caps snippets per hit before flattening.

`raw_results()` returns the unmodified JSON `Dict` (e.g. `{"hits": [...]}` or `{"news": {"results": [...]}}`).

There are also four declarative Pydantic models (`YouHitMetadata`, `YouHit`, `YouAPIOutput`, `YouDocument`) defined at module top — they are **documentation only** and never used in the request/response path.

## 4. Error handling

Minimalist and inconsistent between sync and async:

- **Sync**: `response.raise_for_status()` — raises `requests.HTTPError` on any 4xx/5xx. No retries, no backoff, no logging.
- **Async**: explicit `if res.status == 200 ... else raise Exception(f"Error {res.status}: {res.reason}")` — raises plain `Exception`, not `HTTPError`. Different exception type than sync path.
- **No special handling** of 401 (auth), 429 (rate limit), or 5xx. No retry decorator, no `tenacity`, no logging module use.

The forks should at minimum match this (raise on non-2xx) and ideally improve it with a typed exception class and retry on 429/5xx.

## 5. Param coverage

Exhaustive list of params the wrapper exposes:

- Core: `ydc_api_key`, `endpoint_type` (`"search" | "news" | "rag" | "snippet"`).
- Search & News shared: `num_web_results`, `safesearch` (`"off" | "moderate" | "strict"`), `country`.
- News only: `search_lang`, `ui_lang`, `spellcheck`.
- Post-processing: `k` (max Documents), `n_snippets_per_hit`, `n_hits` (deprecated alias).
- Per-call: `**kwargs` forwarded into query params.

**Not exposed**: `freshness`, `safesearch_strict_terms`, `offset`, custom timeout, custom base URL, custom session, retry config, structured `endpoint_type="research"` (only `rag` is listed but the docstring notes it returns `Forbidden`).

## 6. Retriever vs Tool vs Wrapper relationship

Three layers, strict delegation chain:

```
YouSearchTool._run         ─┐
YouSearchTool._arun        ─┤
YouRetriever._get_relevant_documents   ─┤──► YouSearchAPIWrapper.results / results_async
YouRetriever._aget_relevant_documents  ─┘                       │
                                                                ▼
                                                        raw_results / raw_results_async
                                                                │
                                                                ▼
                                                       requests.get / aiohttp.get
```

Key facts:

- **`YouRetriever(BaseRetriever, YouSearchAPIWrapper)`** — multiple inheritance. The retriever **is** a wrapper; it adds nothing except the LangChain `_get_relevant_documents` callback hooks. It forwards `run_manager.get_child()` into `**kwargs` of `results()` (which is benign because `_generate_params` filters `None` and the kwarg name doesn't collide with any API param — but it is forwarded into the HTTP query string, which is a latent bug).
- **`YouSearchTool(BaseTool)`** — composition, not inheritance. Holds an `api_wrapper: YouSearchAPIWrapper = Field(default_factory=YouSearchAPIWrapper)`. Tool name is `"you_search"`, `args_schema` is a one-field `YouInput(query: str)`. `_run` and `_arun` ignore `run_manager` entirely.

## 7. Code-style notes to mirror

- **Pydantic v2** (`from pydantic import BaseModel, Field, model_validator`). Use `model_validator(mode="before"|"after")` and `typing_extensions.Self` as the return annotation for `mode="after"` validators.
- **Type hints on every parameter and return**. `Optional[X]`, `Literal[...]`, `List[Document]`, `Dict`, `Any`.
- **`langchain_core.documents.Document`** is the universal return type.
- **Env-var resolution** via `langchain_core.utils.get_from_dict_or_env(values, "ydc_api_key", "YDC_API_KEY")` — first attribute name, then env-var name.
- **Docstrings**: NumPy-ish "Attributes" block on the wrapper class (~30 lines listing each attribute with type and one-line description); short Google-style "Args/Returns" on methods. No examples in docstrings.
- **Module docstring** is one short paragraph linking to `documentation.you.com/quickstart`.
- **Async parity**: every public method has an `_async` twin with identical signature.
- **Constants** at module top in `SCREAMING_SNAKE` (`YOU_API_URL`).
- **No logging, no telemetry, no retries** — the forks may improve on this but should not regress.

## 8. Parity table

This is the contract. Each fork must honor every row.

| Field | LangChain wrapper | camel-ai fork must match | gpt-researcher fork must match |
| --- | --- | --- | --- |
| API key env var | `YDC_API_KEY` | `YDC_API_KEY` (canonical; alias `YOU_API_KEY` acceptable) | `YDC_API_KEY` (canonical; alias `YOU_API_KEY` acceptable) |
| API key constructor arg | `ydc_api_key: Optional[str] = None` | `ydc_api_key: Optional[str] = None` | `ydc_api_key: Optional[str] = None` |
| Auth header | `X-API-Key: <key>` | `X-API-Key: <key>` | `X-API-Key: <key>` |
| Base URL | `https://api.ydc-index.io` | same, exposed as overridable constant | same, exposed as overridable constant |
| Endpoint paths | `/search`, `/news`, `/rag`, `/snippet`-aliased-to-search | same set; `endpoint_type` Literal | same set; `endpoint_type` Literal |
| HTTP method | `GET` | `GET` | `GET` |
| Result count param | `num_web_results: Optional[int]` (mapped to `count` for news) | `num_web_results: Optional[int]` | `num_web_results: Optional[int]` |
| Post-fetch cap | `k: Optional[int]` (caps `Document`s) | `k: Optional[int]` | `k: Optional[int]` (or `max_results`) |
| Snippets-per-hit | `n_snippets_per_hit: Optional[int]` | `n_snippets_per_hit: Optional[int]` | `n_snippets_per_hit: Optional[int]` |
| Safesearch | `Literal["off","moderate","strict"]` | same Literal | same Literal |
| Country | `country: Optional[str]` (ISO code) | `country: Optional[str]` | `country: Optional[str]` |
| Lang (News) | `search_lang`, `ui_lang: Optional[str]` | same | same |
| Spellcheck (News) | `spellcheck: Optional[bool]` | same | same |
| Sync API | `raw_results(query)` -> `Dict`, `results(query)` -> `List[Document]` | same names, same returns | gpt-researcher style: `search(query)` accepted, but expose `results()` too |
| Async API | `raw_results_async`, `results_async` | same names | same names |
| Return type | `langchain_core.documents.Document` | LangChain `Document` (or framework-native equivalent w/ same `page_content`+`metadata` shape) | gpt-researcher result schema (`{"href","body"}` style) — **plus** preserve url/title/description/thumbnail_url metadata |
| Document `metadata` keys (search) | `url`, `thumbnail_url`, `title`, `description` | exactly these four keys | exactly these four keys |
| Document `metadata` (news) | full raw result dict | full raw result dict | full raw result dict |
| Error on non-2xx | sync raises `HTTPError`; async raises `Exception` | raise typed exception (improvement OK) | raise typed exception (improvement OK) |
| Param validation | Pydantic v2 + `model_validator` warnings | Pydantic v2 + warnings | Pydantic v2 + warnings |
| Tool name | `you_search` | `you_search` | `you_search` |
| Tool args schema | `YouInput(query: str)` | `YouInput(query: str)` | `YouInput(query: str)` |

## Notable upstream quirks (do NOT mirror)

- The `endpoint_type == "snippet"` → `"search"` rewrite **mutates `self`** before each request — not idempotent across mode changes. Forks should rewrite locally instead.
- The deprecation validator checks `"snippets"` (plural) which is not in the `Literal` — **dead code**.
- `YouRetriever` forwards `run_manager` into `**kwargs` → into HTTP query params. Latent bug; the You.com server presumably ignores unknown params. Forks should drop framework-internal kwargs before building params.
- Sync raises `HTTPError`, async raises bare `Exception` — inconsistent. Forks should unify.
- `n_hits` is a documented-but-unimplemented deprecated alias; the field is declared but never read by `_generate_params`. Forks may omit entirely.

Report file: `research/benchmark-langchain.md` (in https://github.com/dev-mhany/youcom-ecosystem-work)
