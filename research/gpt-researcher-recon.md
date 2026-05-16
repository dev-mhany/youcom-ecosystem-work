# gpt-researcher Recon: Adding a You.com Retriever

Repo: https://github.com/Cooperation-org/gpt-researcher (fork of assafelovic/gpt-researcher; branch `feat/youcom-retriever`, PR https://github.com/Cooperation-org/gpt-researcher/pull/1)
Source: https://github.com/Cooperation-org/gpt-researcher

This report documents the exact patterns to match when adding a `you` retriever to `assafelovic/gpt-researcher`. All file/line refs are repo-relative.

---

## 1. Retriever directory layout

Every retriever lives in its own subdirectory under `gpt_researcher/retrievers/`. Both `__init__.py` files in the package directory are **empty** (0 lines) — they exist only so Python treats the dir as a package; the public class is re-exported one level up.

Tavily layout (our template):

```
gpt_researcher/retrievers/tavily/
├── __init__.py          # 0 lines (empty)
└── tavily_search.py     # 125 lines  -> class TavilySearch
```

Cross-check (other retrievers follow the same pattern):

```
gpt_researcher\retrievers\exa\
├── __init__.py          # 0 lines
└── exa.py               # 101 lines -> class ExaSearch

gpt_researcher\retrievers\xquik\
├── __init__.py          # 0 lines
└── xquik.py             # 94  lines -> class XquikSearch  (newest, closest stylistic peer)

gpt_researcher\retrievers\bocha\
├── __init__.py          # 0 lines
└── bocha.py             # 57  lines -> class BoChaSearch
```

So for You: create `gpt_researcher\retrievers\you\__init__.py` (empty) and `gpt_researcher\retrievers\you\you_search.py` containing `class YouSearch`.

---

## 2. Class contract

### Constructor

There is no formal abstract base class. The de-facto contract, observable across `TavilySearch`, `ExaSearch`, `BoChaSearch`, `XquikSearch`, `SerperSearch`, is a positional `query` plus optional kwargs. The minimum signature compatible with the factory in `actions/retriever.py` and the test runner in `tests/test-your-retriever.py` is:

```python
def __init__(self, query, headers=None, topic="general", query_domains=None, **kwargs):
```

`headers` and `topic` are Tavily-specific extras the framework will pass when available — `headers` carries per-request API keys (see Section 3) and `topic` (Tavily uses `"general"` / `"news"`) is an optional flavor knob. `query_domains` is a list filter that callers may pass.

For the You retriever, mirror Tavily exactly so the framework's call sites Just Work:

```python
def __init__(self, query, headers=None, topic="general", query_domains=None):
```

### `search()` method

`tavily_search.py:100-125` defines the canonical contract:

```python
def search(self, max_results=10):
    ...
    sources = results.get("results", [])
    if not sources:
        raise Exception("No results found with Tavily API search.")
    search_response = [
        {"href": obj["url"], "body": obj["content"]} for obj in sources
    ]
    ...
    return search_response
```

Required:

- Method name: `search`.
- Single keyword arg `max_results: int = <some default>` (Tavily uses 10, BoCha 7, Serper 7).
- Returns: `list[dict]`. Each dict **must** have `href` and `body`. `title` is optional but expected by most consumers (Serper/BoCha/Xquik all set it; Tavily/Exa do not). Add `title` for parity.
- On exception, return `[]` rather than propagating (Tavily pattern, `tavily_search.py:122-124`):

```python
except Exception as e:
    print(f"Error: {e}. Failed fetching sources. Resulting in empty response.")
    search_response = []
return search_response
```

The result list is then consumed by `tests/test-your-retriever.py:41-46` and the framework's scraper pipeline — both treat the list as opaque dicts, only requiring `href`/`body`.

---

## 3. Env-var conventions

Pattern: each retriever reads its key from `os.environ["<NAME>_API_KEY"]`. There are two failure modes used in the codebase:

**Tavily-style — soft fail (warn + return blank):** `tavily_search.py:39-54`

```python
def get_api_key(self):
    api_key = self.headers.get("tavily_api_key")     # per-request override via headers
    if not api_key:
        try:
            api_key = os.environ["TAVILY_API_KEY"]
        except KeyError:
            print(
                "Tavily API key not found, set to blank. If you need a retriver, please set the TAVILY_API_KEY environment variable."
            )
            return ""
    return api_key
```

**Exa/Xquik/Serper-style — hard fail (raise):** `exa.py:24-39`, `xquik.py:28-36`, `serper.py:44-55`

```python
try:
    api_key = os.environ["EXA_API_KEY"]
except KeyError:
    raise Exception(
        "Exa API key not found. Please set the EXA_API_KEY environment variable. "
        "You can obtain your key from https://exa.ai/"
    )
```

Recommendation for You: use the **Tavily soft-fail pattern** including the `headers.get("you_api_key")` per-request override — Tavily is our explicit template and this also supports the multi-tenant header-injection path used by the backend (see `backend/server/server_utils.py:275`). The standard env-var name will be `YOU_API_KEY` (the canonical You.com naming; YDC_API_KEY is also seen in some YDC SDKs but `YOU_API_KEY` matches the user-facing pattern other retrievers use).

`.env.example` (`.env.example:1-4`) currently lists `OPENAI_API_KEY`, `TAVILY_API_KEY`, `XQUIK_API_KEY` — add `YOU_API_KEY=` here too.

---

## 4. Registration in `gpt_researcher/retrievers/__init__.py`

Current file (`gpt_researcher\retrievers\__init__.py`, 35 lines):

```python
1  from .arxiv.arxiv import ArxivSearch
... (alphabetical-ish imports) ...
12 from .tavily.tavily_search import TavilySearch
13 from .exa.exa import ExaSearch
14 from .mcp import MCPRetriever
15 from .bocha.bocha import BoChaSearch
16 from .xquik.xquik import XquikSearch
17
18 __all__ = [
19     "TavilySearch",
...
34     "XquikSearch"
35 ]
```

Changes needed (two single-line additions):

- Insert at line 17 (after the existing imports): `from .you.you_search import YouSearch`
- Insert inside `__all__` after `"XquikSearch"` (line 34): a trailing-comma fix on line 34 then `"YouSearch"` on a new line.

Resulting diff sketch:

```python
 from .xquik.xquik import XquikSearch
+from .you.you_search import YouSearch

 __all__ = [
     ...
-    "XquikSearch"
+    "XquikSearch",
+    "YouSearch",
 ]
```

---

## 5. Runtime selection

Two layers cooperate to pick a retriever.

**Layer A — config parses the `RETRIEVER` env var.** `gpt_researcher\config\config.py:77-83`:

```python
# Handle RETRIEVER with default value
retriever_env = os.environ.get("RETRIEVER", config.get("RETRIEVER", "tavily"))
try:
    self.retrievers = self.parse_retrievers(retriever_env)
except ValueError as e:
    print(f"Warning: {str(e)}. Defaulting to 'tavily' retriever.")
    self.retrievers = ["tavily"]
```

`parse_retrievers` (`config.py:188-201`) splits on commas and validates each name against `get_all_retriever_names()`, which lives in `gpt_researcher\retrievers\utils.py:81-102` and **dynamically scans the directory listing** — meaning that simply creating the `you/` subdirectory automatically makes `RETRIEVER=you` valid. There is also a hardcoded fallback list `VALID_RETRIEVERS` in `utils.py:62-79`:

```python
VALID_RETRIEVERS = [
    "tavily", "custom", "duckduckgo", "searchapi", "serper", "serpapi",
    "google", "searx", "bing", "arxiv", "semantic_scholar",
    "pubmed_central", "exa", "mcp", "xquik", "mock"
]
```

This list is only used if the directory scan errors out. Adding `"you"` to it is belt-and-suspenders — recommended.

**Layer B — factory dispatch.** `gpt_researcher\actions\retriever.py:8-101` is a giant `match` statement mapping the string name to the imported class. New `case` block needed:

```python
case "you":
    from gpt_researcher.retrievers import YouSearch

    return YouSearch
```

Slot it next to the `case "xquik":` block at lines 95-98. Also extend the docstring at `retriever.py:17-33` with a `- you: You.com Search/Research API` line.

User flow (matches docs at `docs\docs\gpt-researcher\search-engines\search-engines.md:14`):

```bash
RETRIEVER=you
YOU_API_KEY=<key>
```

Composable with others:

```bash
RETRIEVER=tavily,you,arxiv
```

---

## 6. Test pattern

There is **no per-retriever pytest suite** in this repo. Two test entry points are relevant:

**A. Live integration smoke test** — `tests/test-your-retriever.py` (49 lines, full file). Runs whatever retriever `RETRIEVER` env var selects and prints results. No mocking. Reproduced verbatim:

```python
import asyncio
from dotenv import load_dotenv
from gpt_researcher.config.config import Config
from gpt_researcher.actions.retriever import get_retrievers
from gpt_researcher.skills.researcher import ResearchConductor
import pprint
load_dotenv()

async def test_scrape_data_by_query():
    config = Config()
    retrievers = get_retrievers({}, config)
    print("Retrievers:", retrievers)

    class MockResearcher:
        def init(self):
            self.retrievers = retrievers
            self.cfg = config
            self.verbose = True
            self.websocket = None
            self.scraper_manager = None
            self.vector_store = None

    researcher = MockResearcher()
    research_conductor = ResearchConductor(researcher)
    sub_query = "design patterns for autonomous ai agents"

    for retriever_class in retrievers:
        retriever = retriever_class(sub_query)
        search_results = await asyncio.to_thread(
            retriever.search, max_results=10
        )
        print("\033[35mSearch results:\033[0m")
        pprint.pprint(search_results, indent=4, width=80)

if __name__ == "__main__":
    asyncio.run(test_scrape_data_by_query())
```

To exercise You: `RETRIEVER=you YOU_API_KEY=<key> python tests/test-your-retriever.py`.

**B. Unit-test style with mocks** — repo uses `unittest` + `unittest.mock` (NOT pytest fixtures). Template from `tests/test_quick_search.py:1-46`:

```python
import unittest
from unittest.mock import MagicMock, patch, AsyncMock
import asyncio
from gpt_researcher.agent import GPTResearcher
import os

class TestQuickSearch(unittest.TestCase):
    @patch('gpt_researcher.agent.get_search_results', new_callable=AsyncMock)
    @patch('gpt_researcher.agent.create_chat_completion', new_callable=AsyncMock)
    @patch('langchain_openai.OpenAIEmbeddings')
    def test_quick_search_no_summary(self, mock_embeddings, mock_create_chat, mock_search):
        mock_search.return_value = [{'title': 'Test Result', 'content': 'Content', 'url': 'http://test.com'}]
        ...

if __name__ == '__main__':
    unittest.main()
```

For YouSearch unit tests, mock `requests.post` (or whichever HTTP primitive you use) with `unittest.mock.patch`, assert the URL/headers/payload, and assert the returned list shape `[{"href":..., "body":..., "title":...}]`.

---

## 7. Multilingual / region surface

**Global config knob** — `gpt_researcher/config/variables/default.py:28` ships `"LANGUAGE": "english"` and it's exposed as `cfg.language`. It is consumed by the **report writer**, not retrievers (`gpt_researcher/actions/report_generation.py:254,258`). The retrievers do not currently read `cfg.language`.

**Per-retriever locale** — only Serper exposes locale knobs today (`gpt_researcher/retrievers/serper/serper.py:13-29`):

```python
def __init__(self, query, query_domains=None, country=None, language=None, time_range=None, exclude_sites=None):
    ...
    self.country = country or os.getenv("SERPER_REGION")
    self.language = language or os.getenv("SERPER_LANGUAGE")
    self.time_range = time_range or os.getenv("SERPER_TIME_RANGE")
```

Pattern documented in `docs\docs\gpt-researcher\search-engines\search-engines.md:89-96`. DuckDuckGo hardcodes `region='wt-wt'` (`duckduckgo.py:25`).

**Implication for You:** since You.com supports `country` and `safe_search` parameters, follow the Serper precedent and accept ctor kwargs `country`, `safe_search` falling back to `YOU_COUNTRY`, `YOU_SAFE_SEARCH` env vars. This keeps the surface consistent and makes the multilingual story land cleanly.

---

## 8. Concrete drop-in plan

In order:

1. **Create** `gpt_researcher/retrievers/you/__init__.py` — empty file (0 bytes), matching all peers.
2. **Create** `gpt_researcher/retrievers/you/you_search.py` containing `class YouSearch`. Mirror `tavily_search.py` line-for-line:
   - `__init__(self, query, headers=None, topic="general", query_domains=None)` plus optional `country`, `safe_search` (Serper-style locale support).
   - `get_api_key()` reads `headers.get("you_api_key")` then falls back to `os.environ["YOU_API_KEY"]`; soft-fail with print + return `""`.
   - Internal `_search()` POST/GET to You.com endpoint (Search API: `https://api.ydc-index.io/search`; Research API: `https://chat-api.you.com/research` — pick based on which surface this retriever wraps; recommend Search API for parity with Tavily, with `topic` switching to Research API when set to `"research"`).
   - Public `search(self, max_results=10)` returns `[{"href": ..., "body": ..., "title": ...}, ...]`; `try/except` returns `[]` on failure.
3. **Modify** `gpt_researcher/retrievers/__init__.py`:
   - Add `from .you.you_search import YouSearch` after line 16 (after the `XquikSearch` import).
   - Add `"YouSearch"` to `__all__` after `"XquikSearch"` (line 34) — also append the missing trailing comma to line 34.
4. **Modify** `gpt_researcher/actions/retriever.py`:
   - Add `case "you":` block returning `YouSearch` after the `case "xquik":` block (lines 95-98).
   - Add `- you: You.com Search/Research API` to the docstring at lines 17-33.
5. **Modify** `gpt_researcher/retrievers/utils.py`:
   - Add `"you"` to `VALID_RETRIEVERS` at lines 62-79 (defensive — the dir scan at `get_all_retriever_names()` already picks it up dynamically). Insert before `"mock"`.
6. **Modify** `.env.example`:
   - Add `YOU_API_KEY=` after the existing `XQUIK_API_KEY=` line (line 3).
   - Optional: `YOU_COUNTRY=`, `YOU_SAFE_SEARCH=` if implementing locale knobs.
7. **Modify** `docs/docs/gpt-researcher/search-engines/search-engines.md`:
   - Add `- [You.com](https://you.com/) - Env: \`RETRIEVER=you\`` to the supported list at lines 26-36.
8. **Create** unit test `tests/test_you_retriever.py`:
   - `unittest.TestCase` subclass, `unittest.mock.patch` on `requests.post` (or `requests.get`).
   - Cases: (a) success returns shape, (b) missing `YOU_API_KEY` -> empty list + warning print, (c) HTTP 4xx -> empty list, (d) `query_domains` filter is forwarded to API payload.
   - Run with `python -m unittest tests.test_you_retriever`.
9. **(No change needed)** `gpt_researcher/config/variables/base.py` and `default.py` — `RETRIEVER` is already a free-form `str` validated dynamically against the directory scan; no schema change needed to accept `"you"`.

---

## Key reference files (repo-relative paths)

- `gpt_researcher/retrievers/__init__.py` (35 lines) — registration table
- `gpt_researcher/retrievers/tavily/tavily_search.py` (125 lines) — primary template
- `gpt_researcher/retrievers/exa/exa.py` (101 lines) — cross-validation
- `gpt_researcher/retrievers/xquik/xquik.py` (94 lines) — newest peer, idiomatic style
- `gpt_researcher/retrievers/serper/serper.py` (131 lines) — locale/region precedent
- `gpt_researcher/retrievers/utils.py` (103 lines) — `VALID_RETRIEVERS` + dynamic discovery
- `gpt_researcher/actions/retriever.py` (152 lines) — factory `match` block + `get_retrievers`
- `gpt_researcher/config/config.py` (313 lines) — `RETRIEVER` env parsing at lines 77-83, 188-201
- `gpt_researcher/config/variables/default.py` (54 lines) — defaults incl. `"RETRIEVER": "tavily"` and `"LANGUAGE": "english"`
- `tests/test-your-retriever.py` (49 lines) — live smoke test entry point
- `tests/test_quick_search.py` (46 lines) — unittest+mock template
- `.env.example` — env var registration
- `docs/docs/gpt-researcher/search-engines/search-engines.md` — user-facing retriever catalog
