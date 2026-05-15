# gpt-researcher: You.com Retriever Build Report

Build date: 2026-05-15
Repo: `E:\youdotcom\workspace\gpt-researcher-fork\` (branch `main`, 8 commits ahead of `origin/main`, working tree clean, **not pushed**)

## Commits

```
a319d30a Document You.com retriever in search-engines guide
077b8c63 Add unit tests for YouSearch
9bec63bc Add HTTP error handling
ca93f2aa Add country and language locale support
8b9e208a Wire YouSearch into retrievers registry
b531be50 Add soft-fail on missing API key (Tavily pattern)
d0044789 Implement YouSearch core retrieval
296826ff Scaffold You.com retriever package
```

Author: Muhammad Hany <dev.mhany@gmail.com> (per existing repo git config — not modified).

## Files touched

| File | Change | Lines |
|---|---|---|
| `gpt_researcher/retrievers/you/__init__.py` | new | 0 |
| `gpt_researcher/retrievers/you/you_search.py` | new | +148 |
| `gpt_researcher/retrievers/__init__.py` | re-export `YouSearch` + add to `__all__` | +3 / -1 |
| `gpt_researcher/actions/retriever.py` | new `case "you":` + docstring | +5 |
| `gpt_researcher/retrievers/utils.py` | append `"you"` to `VALID_RETRIEVERS` | +1 |
| `tests/test_you_retriever.py` | new (6 unittest cases) | +181 |
| `docs/docs/gpt-researcher/search-engines/search-engines.md` | catalog entry + Setup section | +22 |

`git diff --shortstat origin/main..HEAD`: **7 files changed, 360 insertions(+), 1 deletion(-)**.

LOC added by category:
- Production code: 157 (148 retriever + 9 wiring across 3 files)
- Tests: 181
- Docs: 22

## Test run

`python -m unittest tests.test_you_retriever` — **6/6 passing** in ~10ms.

```
test_search_propagates_locale ... ok
test_search_returns_empty_on_http_error ... ok
test_search_returns_empty_on_timeout ... ok
test_search_returns_results_in_tavily_shape ... ok
test_search_soft_fails_on_missing_key ... ok
test_search_uses_header_key_over_env ... ok
```

The repo's existing tests (e.g. `test_quick_search.py`) cannot run in the local Python 3.13 env because the full `requirements.txt` (LangChain v1, litellm, etc.) is not installed and pulling 69 packages was out of scope. Our test deliberately avoids that dep chain by importing the retriever module via `importlib.util.spec_from_file_location` — the You retriever itself only depends on stdlib + `requests`, which is already present.

## Deviations from plan

1. **`logger.warning` instead of `print`.** The Tavily pattern uses bare `print` with no logging import. The brief asked for `logger.warning(...)` while also saying "match Tavily verbatim". I treated this as an intentional improvement (Tavily's `print` is a known wart) and matched Tavily's *phrasing* — kept the original "set to blank. If you need a retriver, please set the YOU_API_KEY environment variable" copy (typo "retriver" preserved deliberately to mirror Tavily). Standard `logging.getLogger(__name__)` per the project convention in `retrievers/utils.py`.

2. **Test import strategy.** Original plan had a direct `from gpt_researcher.retrievers.you.you_search import ...`. That triggers `gpt_researcher/__init__.py` → `agent.py` → langchain/aiofiles/etc. — none of which were installed. Switched to `importlib.util.spec_from_file_location` so the test runs against the retriever module in isolation. This is structurally correct and does not weaken the test (the patch targets are still resolved against the real source file). It also keeps the tests fast and dep-light — actually more in keeping with the gpt-researcher tradition of "the tests don't always run", but with the upside that ours actually do.

3. **Docs location.** Spec said "Likely top-level `README.md` has a 'Supported Retrievers' section. If so, add a `you` entry there." The top-level `README.md` does *not* have one — its only retriever mentions are inline `RETRIEVER=tavily,mcp` examples. The actual catalog lives in `docs/docs/gpt-researcher/search-engines/search-engines.md` (38-line list of supported engines + per-engine setup sections). I edited that file instead, matching the existing Serper section's tone and length exactly.

4. **`YOU_SAFE_SEARCH` env-var name.** Per the locked decisions section, the env-var for the language kwarg is `YOU_SAFE_SEARCH`. This is semantically odd (a language code stored under a "safe search" name) but is the explicit locked decision and is also repeated in the commit-5 instructions. I implemented it as specified. **Open question** — see below.

## Open questions for human review

1. **`YOU_SAFE_SEARCH` is a confusing name for a language env var.** The locked spec maps the `language` ctor kwarg to `YOU_SAFE_SEARCH` in the env, claiming this mirrors Serper's `SERPER_LANGUAGE`. It does not — `SERPER_LANGUAGE` is named for what it carries. I suspect this was meant to be `YOU_LANGUAGE` (matching `YOU_COUNTRY`) and `YOU_SAFE_SEARCH` was a stray third env var that got conflated with the language one. Recommend either renaming to `YOU_LANGUAGE` or actually using `YOU_SAFE_SEARCH` for a real `safesearch` ctor kwarg (`off`/`moderate`/`strict` per the API reference). Doc copy makes the same mapping; if this is changed, the docs and tests need a coordinated update.

2. **Whether to add `YOU_API_KEY=`/`YOU_COUNTRY=`/`YOU_SAFE_SEARCH=` to `.env.example`.** The recon recommended it but it wasn't in the 8-commit plan, so I left it. One-line additions if desired.

3. **Whether to extend the `topic` argument** (currently accepted but unused) to switch between Search API and Research API per the recon's section 8.2 idea. Out of scope for this 8-commit plan but a natural next iteration — would add a `topic="research"` branch that POSTs to `https://api.you.com/v1/research` with `research_effort` defaulting to `"lite"`.

4. **The smoke-test entry point `tests/test-your-retriever.py`** does not need code changes — it dynamically uses whatever `RETRIEVER` env var selects. To exercise live: `RETRIEVER=you YOU_API_KEY=<key> python tests/test-your-retriever.py`. Not run as part of this build (no live calls — explicit constraint).

## Verification checklist

- [x] 8 commits, exact subjects from the plan
- [x] `git status` clean in `gpt-researcher-fork`
- [x] No push to remote (`Your branch is ahead of 'origin/main' by 8 commits`)
- [x] All 6 unit tests pass
- [x] No files modified outside `gpt_researcher/retrievers/you/**`, `gpt_researcher/retrievers/__init__.py`, `gpt_researcher/retrievers/utils.py`, `gpt_researcher/actions/retriever.py`, `tests/test_you_retriever.py`, `docs/docs/gpt-researcher/search-engines/search-engines.md`
- [x] No files modified in `youcom-ecosystem-work` except this report
