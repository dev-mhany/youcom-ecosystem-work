# CAMEL Search Toolkit — Recon Report (for You.com Search integration)

Repo: https://github.com/Cooperation-org/camel (fork of camel-ai/camel; branch `feat/youcom-search`, PR https://github.com/Cooperation-org/camel/pull/1)
Source: https://github.com/Cooperation-org/camel
Target file: `camel/toolkits/search_toolkit.py` (1703 lines)

All references below are `<repo-relative path>:<line>`.

---

## 1. Existing `search_*` functions in `search_toolkit.py`

All live as methods on a single `SearchToolkit(BaseToolkit)` class declared at `search_toolkit.py:33`. The class is decorated with `@MCPServer()` (search_toolkit.py:32) so every method is auto-exposed as an MCP tool too.

| Function | Line | Decorators | Query param | Locale params | Returns |
|---|---|---|---|---|---|
| `search_serper` | 63 | `@api_keys_required([(None, "SERPER_API_KEY")])` | `query` | `location: str = "United States"` | `Dict[str, Any]` |
| `search_wiki` | 113 | `@dependencies_required("wikipedia")` | `entity` | none | `str` |
| `search_linkup` | 152 | `@dependencies_required("linkup")` + `@api_keys_required([(None, "LINKUP_API_KEY")])` | `query` | none | `Dict[str, Any]` |
| `search_duckduckgo` | 223 | `@dependencies_required("ddgs")` | `query` | none | `List[Dict[str, Any]]` |
| `search_brave` | 321 | `@api_keys_required([(None, 'BRAVE_API_KEY')])` | **`q`** (not `query`) | `country`, `search_lang`, `ui_lang` | `Dict[str, Any]` |
| `search_google` | 503 | `@api_keys_required([(None, 'GOOGLE_API_KEY'), (None, 'SEARCH_ENGINE_ID')])` | `query` | hard-coded `"en"` (search_toolkit.py:607) | `List[Dict[str, Any]]` |
| `search_tavily` | 737 | none (does its own env-var check) | `query` | none, takes `**kwargs` | `List[Dict[str, Any]]` |
| `search_bocha` | 791 | `@api_keys_required([(None, 'BOCHA_API_KEY')])` | `query` | none | `Dict[str, Any]` |
| `search_baidu` | 858 | none | `query` | none (Chinese-only via scrape) | `Dict[str, Any]` |
| `search_bing` | 933 | none | `query` | none (CN bing) | `Dict[str, Any]` |
| `search_exa` | 1046 | `@api_keys_required([(None, 'EXA_API_KEY')])` | `query` | none | `Dict[str, Any]` |
| `search_alibaba_tongxiao` | 1168 | `@api_keys_required([(None, 'TONGXIAO_API_KEY')])` | `query` | none | `Dict[str, Any]` |
| `search_metaso` | 1336 | `@api_keys_required([(None, 'METASO_API_KEY')])` | `query` | none | `Dict[str, Any]` |
| `search_serpapi` | 1404 | `@dependencies_required("google-search-results")` + `@api_keys_required([(None, 'SERPAPI_KEY')])` | `query` | `gl` (country), `search_lang` (`hl`), `location`, `google_domain` | `Dict[str, Any]` |
| `search_querit` | 1502 | `@api_keys_required([(None, 'QUERIT_API_KEY')])` | `query` | `country_include: List[str]`, `language_include: List[str]` | `Dict[str, Any]` |

Docstrings everywhere use `r"""..."""` (raw), Google style with `Args:` / `Returns:`. Default values are surfaced inline as `(default: :obj:`<value>`)`. Per-line max width is 79 (ruff line-length = 79, see `pyproject.toml:519`), but `search_toolkit.py` is whitelisted for E501 at `pyproject.toml:552` so long URL/docstring lines are fine.

---

## 2. The pattern a new `search_you()` should follow

The dominant template (Serper, Bocha, Brave, Querit) is **plain `requests` + decorator + try/except returning an error dict**. No retry helper, no shared client. Use this skeleton:

```python
@api_keys_required([(None, 'YOU_API_KEY')])
def search_you(
    self,
    query: str,
    number_of_result_pages: int = 10,
    country: Optional[str] = None,        # e.g. "US", "JP", "AE"
    search_lang: Optional[str] = None,    # e.g. "en", "ja", "ar"
    safesearch: str = "moderate",
    freshness: Optional[str] = None,
) -> Dict[str, Any]:
    r"""Use You.com Search API to search information for the given query.

    Args:
        query (str): The search query string.
        number_of_result_pages (int): The number of result pages to
            retrieve. (default: :obj:`10`)
        ...

    Returns:
        Dict[str, Any]: ...
    """
    YOU_API_KEY = os.getenv("YOU_API_KEY")

    url = "https://api.ydc-index.io/search"
    headers = {
        "X-API-Key": YOU_API_KEY,
        "Content-Type": "application/json",
    }
    params = {
        "query": query,
        "num_web_results": number_of_result_pages,
        # ... plus optional params, dropped if None (see Brave at :464)
    }
    params = {k: v for k, v in params.items() if v is not None}

    try:
        response = requests.get(
            url, headers=headers, params=params, timeout=self.timeout
        )
        if response.status_code != 200:
            return {
                "error": (
                    f"You.com API failed with status {response.status_code}: "
                    f"{response.text}"
                )
            }
        return response.json()
    except requests.exceptions.RequestException as e:
        return {"error": f"You.com search failed: {e!s}"}
```

Required imports already present at top of `search_toolkit.py:14-27`:

```python
import os
import warnings
from typing import Any, Dict, List, Literal, Optional, TypeAlias, Union, cast
import requests
from camel.logger import get_logger
from camel.toolkits.base import BaseToolkit
from camel.toolkits.function_tool import FunctionTool
from camel.utils import (MCPServer, api_keys_required, dependencies_required)
```

Conventions observed across all functions:

- **Env-var convention**: ALL CAPS, suffix `_API_KEY` (e.g. `BOCHA_API_KEY`, `BRAVE_API_KEY`, `TAVILY_API_KEY`, `QUERIT_API_KEY`). Matching local var inside function is the SAME name (`BOCHA_API_KEY = os.getenv("BOCHA_API_KEY")`, `search_toolkit.py:825`). Use **`YOU_API_KEY`**.
- **Decorator order**: `@dependencies_required(...)` (outer) then `@api_keys_required(...)` (inner) — see `search_toolkit.py:1402-1403`. We do not need `dependencies_required` since we'll just use `requests`.
- **Timeout**: always `timeout=self.timeout` (e.g. `search_toolkit.py:99, 467, 845, 891`). `self.timeout` is set by `BaseToolkit.__init__` (`base.py:73`).
- **Logging**: `logger = get_logger(__name__)` at module level (`search_toolkit.py:29`). Use `logger.warning/info/error/debug`. Never `print` — see CONTRIBUTING `:253-269`.
- **Error contract**: return `{"error": "..."}` for `Dict` returns, or `[{"error": "..."}]` for `List` returns. Do not raise (Brave at `:472-474` is the lone exception that raises `RuntimeError`).
- **Optional-param dropping**: build full param dict then `{k: v for k, v in params.items() if v is not None}` — see Brave at `:464`.
- **Param naming**: prefer `query: str` (Brave is the outlier with `q`). Use `number_of_result_pages: int = 10` for result count to match the codebase convention (Bocha :797, Brave :337, Tavily :738, etc.).

No retry logic exists in any search function. No httpx. No async helper. No shared session.

---

## 3. Toolkit registration

Three layers:

1. **Class-level**: `@MCPServer()` decorator at `search_toolkit.py:32` auto-registers all methods as MCP tools.
2. **Instance-level**: `get_tools()` at `search_toolkit.py:1668` returns a `List[FunctionTool]`, one per `search_*` method:

   ```python
   def get_tools(self) -> List[FunctionTool]:
       return [
           FunctionTool(self.search_serper),
           FunctionTool(self.search_wiki),
           ...
           FunctionTool(self.search_querit),
       ]
   ```

   `FunctionTool` (imported from `camel.toolkits.function_tool` at `:22`) auto-generates the OpenAI function schema from the method's signature + docstring. **No manual schema needed** — just register the new method here.

3. **Package export**: `camel/toolkits/__init__.py:25` already does `from .search_toolkit import SearchToolkit` and exports it in `__all__` at `:123`. **No change needed** to `__init__.py` for adding `search_you` (we are not adding a new toolkit class, just a method).

---

## 4. Test pattern

Test file: `test/toolkits/test_search_functions.py` (1214 lines). One file holds tests for every search engine; tests grouped by section banners like `# ==================== Querit Search Tests ====================` (`:1010`). Mocking style: `unittest.mock` (`patch`, `MagicMock`, `call`) — no `pytest-mock`, no `responses`, no `respx`. Imports at `:14-22`.

Shared fixture at `:25-27`:

```python
@pytest.fixture
def search_toolkit():
    return SearchToolkit()
```

Naming convention: `test_search_<engine>[_<scenario>]`, e.g. `test_search_querit_success`, `test_search_querit_with_filters`, `test_search_querit_http_error`. Verbatim representative test (`test/toolkits/test_search_functions.py:1013-1071`):

```python
@patch('requests.post')
def test_search_querit_success(mock_post, search_toolkit):
    """Test successful Querit search."""
    mock_response = MagicMock()
    mock_response.status_code = 200
    mock_response.json.return_value = {
        "took": "120ms",
        "error_code": 200,
        "error_msg": "",
        "search_id": 12345,
        "query_context": {"query": "CAMEL-AI"},
        "results": {
            "result": [
                {
                    "url": "https://www.camel-ai.org/",
                    "page_age": "2025-01-15T10:30:00Z",
                    "title": "CAMEL-AI: First Multi-Agent Framework",
                    "snippet": "CAMEL-AI is an open-source framework...",
                    "site_name": "camel-ai.org",
                    "site_icon": "https://www.camel-ai.org/favicon.ico",
                },
                ...
            ]
        },
    }
    mock_post.return_value = mock_response

    with patch.dict(os.environ, {'QUERIT_API_KEY': 'fake_api_key'}):
        result = search_toolkit.search_querit(query="CAMEL-AI")

    assert "results" in result
    assert len(result["results"]) == 2
    assert result["took"] == "120ms"
    assert result["search_id"] == 12345
    assert result["results"][0] == {
        "result_id": 1,
        "title": "CAMEL-AI: First Multi-Agent Framework",
        ...
    }

    mock_post.assert_called_once()
    args, kwargs = mock_post.call_args
    assert args[0] == "https://api.querit.ai/v1/search"
    assert kwargs['headers']['Authorization'] == 'Bearer fake_api_key'
    assert kwargs['headers']['Content-Type'] == 'application/json'
    assert 'timeout' in kwargs
```

Key patterns:
- `@patch('requests.post')` (or `'requests.get'`) at module level — patches the `requests` import inside `search_toolkit.py`.
- `with patch.dict(os.environ, {'<NAME>_API_KEY': 'fake_api_key'}):` to satisfy the `@api_keys_required` decorator.
- Assert URL, headers, payload, and `'timeout' in kwargs`.
- Error tests follow `test_search_querit_http_error` (`:1144`), `test_search_querit_request_exception` (`:1183`), `test_search_querit_empty_results` (`:1195`) — copy these shapes.

Existing test files for search live in **one consolidated file**, not per-engine. There is **no** existing `test_search_toolkit_<engine>.py`; pattern is to append to `test_search_functions.py`.

---

## 5. Multilingual / locale surface (relevant to AR / JA story)

Most engines do NOT expose locale params. The ones that do:

- **Brave** (`search_toolkit.py:321-431`): `country` (ISO-2 like `US`, `JP`), `search_lang` (uses Brave's non-standard codes — `'ar'`, `'jp'` (not `'ja'`!), `'zh-hans'` — see docstring at `:351-358`), `ui_lang` (e.g. `'en-US'`, `'jp-JP'`, `'ar-SA'`).
- **SerpApi** (`search_toolkit.py:1404`): `gl: str = "us"` (country), `search_lang: str = "en"` (maps to Google `hl`), `google_domain`, `location`.
- **Querit** (`search_toolkit.py:1502`): `country_include: Optional[List[str]]` (long names — `"japan"`, `"south korea"` — see `:1547-1552`), `language_include: Optional[List[str]]` (`"english"`, `"japanese"`, `"korean"`, `"german"`, `"french"`, `"spanish"`, `"portuguese"` — see `:1554-1558`). **No Arabic option.**
- **Serper** (`search_toolkit.py:67`): `location: str = "United States"` only.
- **Alibaba Tongxiao** (`search_toolkit.py:1168`): no explicit lang, but `industry` is region-flavored Chinese categories.
- **Google** (`search_toolkit.py:503`): hard-coded `search_language = "en"` at `:607` — no parameter exposed. (Implicit anti-pattern; You.com integration should beat this.)

**Opportunity**: You.com supports `country` (ISO-2, two-letter — `US`, `JP`, `AE`, `SA`, etc.) and `search_lang` (BCP-47-ish `en`, `ja`, `ar`). Recommend exposing both with `Optional[str] = None` defaults so we beat Google (no locale at all) and beat Brave (Brave uses `'jp'` not `'ja'` and offers no Arabic country code mapping). For Arabic specifically the Brave docstring lists `'ar'` as a `search_lang` value but no Arab country in `Querit`'s list — so You.com is the cleanest win for `ar-SA` / `ar-AE` use cases.

---

## 6. Concrete drop-in plan

Execute in this order:

1. **Modify** `camel/toolkits/search_toolkit.py`:
   - Insert `search_you()` method between `search_tavily()` (ends `:788`) and `search_bocha()` (starts `:790`). Use the skeleton in section 2.
   - Append `FunctionTool(self.search_you),` to the list returned by `get_tools()` — insert between `FunctionTool(self.search_tavily),` (`:1682`) and `FunctionTool(self.search_brave),` (`:1683`) to keep the order consistent with the method order.

2. **No change** to `camel/toolkits/__init__.py` — `SearchToolkit` is already exported at `:25`/`:123`. We are not creating a new toolkit class.

3. **Modify** `test/toolkits/test_search_functions.py`:
   - Append a new section (banner-comment `# ==================== You.com Search Tests ====================`) at the end of the file (current EOF `:1214`). Add at least: `test_search_you_success`, `test_search_you_with_locale` (covers `country='JP'`, `search_lang='ja'`, plus an `ar` case), `test_search_you_http_error`, `test_search_you_request_exception`. Mock `requests.get` (or `.post` depending on which You.com endpoint is used) following the Querit pattern at `:1013-1183`.
   - **Do NOT** create a separate `test_search_toolkit_you.py` — the project keeps all search tests in this one file.

4. **No change** to `pyproject.toml`:
   - `requests` is already present transitively (it's in `uv.lock` at line 9575 and used directly throughout `search_toolkit.py`). No new dep needed.
   - The toolkit's `[tool.ruff.lint.per-file-ignores]` entry for `search_toolkit.py = ["E501"]` (`pyproject.toml:552`) already covers long lines.
   - If You.com requires an SDK (`youcom` / `you-search`), add it to the `web_tools` optional group at `pyproject.toml:96-120` and decorate the new method with `@dependencies_required("<pkg>")`. Plain `requests` is preferred per the dominant pattern; do not add an SDK unless necessary.

5. **License header**: every Python file in the repo starts with the 13-line CAMEL-AI copyright block (e.g. `search_toolkit.py:1-13`). Any new test file would need it; since we're appending to existing files, no header work needed.

6. **Style checklist** (CONTRIBUTING `:183-251`):
   - `r"""..."""` raw docstrings, Google style.
   - Toolkit-prefix naming — `search_you` matches the existing `search_<engine>` pattern.
   - 79-char line limit for new code (the E501 ignore is escape-hatch only).
   - Use `logger`, never `print`.
   - No abbreviations (`number_of_result_pages`, not `num_pages`).

---

## Quick-reference file table

| Path | Purpose |
|---|---|
| `camel/toolkits/search_toolkit.py` | Add `search_you()` method + register in `get_tools()` |
| `camel/toolkits/__init__.py` | No change |
| `camel/toolkits/base.py` | Reference only — `BaseToolkit.__init__(timeout=...)` at `:73` |
| `camel/utils/commons.py` | Reference only — `api_keys_required` decorator at `:248` |
| `test/toolkits/test_search_functions.py` | Append You.com test section |
| `pyproject.toml` | No change unless we add an SDK |
| `CONTRIBUTING.md` | Style reference (raw docstrings, prefix naming, logger, 79-char) |
