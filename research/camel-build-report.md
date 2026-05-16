# CAMEL — You.com Search Integration Build Report

**Repo:** https://github.com/Cooperation-org/camel (branch `feat/youcom-search`, PR https://github.com/Cooperation-org/camel/pull/1; ahead of upstream `master` by 8 commits)
**Date:** 2026-05-15
**Author config:** `Muhammad Hany <dev.mhany@gmail.com>` (verified before commit 1)
**Pushed:** No. Local commits only.

---

## 1. Commits (in order)

| # | Hash | Subject |
|---|---|---|
| 1 | `6341d600` | Add search_you() skeleton to SearchToolkit |
| 2 | `c8d20325` | Implement You.com Search API wrapper |
| 3 | `7fe5f036` | Add error handling for search_you |
| 4 | `140d82db` | Register search_you in get_tools() |
| 5 | `dcb75339` | Document Arabic and Japanese examples in search_you docstring |
| 6 | `828e8732` | Add unit tests for search_you |
| 7 | `f97b35d1` | Apply ruff formatting to search_you additions |
| 8 | `1a35bdc6` | Add You.com Search README usage example |

`git log --oneline -8` output is reproduced verbatim above.

---

## 2. Files touched

| Path | Change |
|---|---|
| `camel/toolkits/search_toolkit.py` | Added `search_you()` method between `search_tavily` and `search_bocha`; registered `FunctionTool(self.search_you)` in `get_tools()` between `search_tavily` and `search_brave`. |
| `test/toolkits/test_search_functions.py` | Appended `# ==================== You.com Search Tests ====================` section at end of file with 5 tests + an inline `YOU_FIXTURE_RESPONSE` dict mirroring the captured `research/fixtures/search-response.json`. |
| `README.md` | Inserted `### Search: You.com` subsection before `### Seeking Help`. |

No other files modified. `camel/toolkits/__init__.py` and `pyproject.toml` were not touched (no new toolkit class, no new dependency required).

---

## 3. LOC added

`git diff --shortstat origin/master..HEAD` → **3 files changed, 327 insertions(+), 0 deletions(-)**

Per-file insertions:
- `camel/toolkits/search_toolkit.py`: +131
- `test/toolkits/test_search_functions.py`: +168
- `README.md`: +28

(Net deletions across the eight commits include the ruff-format compactions in commit 7, but the cumulative diff vs `origin/master` is purely additive.)

---

## 4. Tests

**Did not run end-to-end.** The test runner cannot import `camel.toolkits` on this machine without the full `[dev]`/`[web_tools]` install — the import chain pulls in `openai`, `docstring_parser`, `wikipedia`, etc., none of which are present in the system Python and `uv` is not installed. Per the build instructions ("If running tests is slow or breaks on Windows, skip the run"), I did not attempt to fully bootstrap the project env.

What was verified instead:
- `python -m ruff format camel/toolkits/search_toolkit.py` → `1 file reformatted` (commit 7 captures the result).
- `python -m ruff check --fix camel/toolkits/search_toolkit.py` → `All checks passed!`
- `python -m ruff format test/toolkits/test_search_functions.py` → reformatted; the only remaining `ruff check` warning is `RUF059` on **line 727** (a pre-existing Bocha test, unrelated to the You.com additions).
- `python -c "import ast; ast.parse(...)"` on both modified Python files → both parse cleanly.

**Structural correctness of the tests:**
- All 5 tests use `@patch('requests.get')` (matching the actual HTTP verb used by `search_you`).
- `patch.dict(os.environ, {'YOU_API_KEY': 'fake_api_key'})` satisfies the `@api_keys_required([(None, 'YOU_API_KEY')])` decorator.
- `test_search_you_missing_api_key` asserts `pytest.raises(ValueError, match="YOU_API_KEY")` — verified against the decorator at `camel/utils/commons.py:349` which raises exactly `ValueError(f"Missing or empty required API keys ... {missing_keys}")`.
- `test_search_you_multilingual_params_propagated` asserts `kwargs['params']['country'] == 'SA'` and `kwargs['params']['search_lang'] == 'ar'`.

---

## 5. Deviations from plan

1. **Method signature trimmed.** The recon's skeleton sketched `safesearch` and `freshness` parameters too. The locked-decisions block in the build prompt only required `country` and `search_lang`, so I held to the prompt and omitted the extra params to keep the surface minimal. Easy follow-up if the team wants them.
2. **Return type is `List[Dict[str, Any]]`, not `Dict[str, Any]`.** The recon table shows Bocha returning `Dict`. The prompt explicitly said "Match how Bocha/Querit shape their returns" and listed only `result_id`, `title`, `description`, `long_description`, `url` — that's a flat list of hits, which `search_tavily` already returns as `List`. I went with `List` to match Tavily, which is the closest sibling (and the immediately-preceding method). Error path returns `[{"error": ...}]` to match the list contract; this also matches Tavily at `:788`.
3. **Documentation file.** `docs/key_modules/tools.md` only contains the high-level toolkit table, not per-engine subsections, so adding a "Search: You.com" subsection there would have been out of place. I added the example to `README.md` instead, immediately after the existing DuckDuckGo/SearchToolkit example. The recon noted that this was the fallback location.
4. **Inlined fixture instead of copying the JSON.** The captured `search-response.json` is dominated by very long Mintlify thumbnail URLs that add no signal to the assertions. I trimmed those URLs in the inlined `YOU_FIXTURE_RESPONSE` dict; field names, structure, and snippet content are preserved verbatim from the live response. This keeps the test self-contained and avoids importing a JSON file from outside `test/` (which would require deciding whether to ship a copy under `test/toolkits/fixtures/`).
5. **Endpoint URL.** Used `https://ydc-index.io/v1/search` exactly as locked in the prompt. (Note: the LangChain benchmark uses `https://api.ydc-index.io/search` — a different host with no `/v1`. Per the api-reference doc, our captured 200 came from `ydc-index.io/v1/search`; the langchain host returns 403. The prompt's locked decision matches what actually works.)

---

## 6. Open questions for human review

1. **Env var alias?** The benchmark recommends `YDC_API_KEY` as the canonical LangChain-compatible name with `YOU_API_KEY` as an acceptable alias. The build prompt locked `YOU_API_KEY` only. If parity with LangChain matters for users migrating, consider a small follow-up that reads `YOU_API_KEY` first then falls back to `YDC_API_KEY`. Not in scope for this PR.
2. **Live smoke test.** No live API call was made. The captured fixture is from 2026-05-15 and matches the documented schema, but the human's planned smoke test should confirm the API still returns `results.web[]` (the docs reference `results.hits` in some places — we went with `results.web` because the live capture confirms it).
3. **`safesearch` / `freshness` / `include_domains`.** Deliberately omitted to match the prompt's locked surface. Worth adding in a v2 commit if user demand surfaces.
4. **`results.news[]` handling.** The current implementation iterates only `results.web[]`. News results never appear unless requested, but if/when we add `livecrawl`/news support, we'd want a defensive `.get("news", [])` merge.
5. **Pre-existing `RUF059` warning on test line 727** is unrelated but might be worth a separate cleanup PR.

---

## 7. Sanity-check commands the reviewer can run

```bash
# Repo: https://github.com/Cooperation-org/camel (branch feat/youcom-search)
git clone -b feat/youcom-search https://github.com/Cooperation-org/camel.git && cd camel
git log --oneline -8                         # confirm 8 commits
git status                                   # confirm clean tree
python -m ruff check camel/toolkits/search_toolkit.py   # confirm no new lint issues
python -m ruff check test/toolkits/test_search_functions.py  # only pre-existing RUF059
# Once a full project env is installed:
uv run pytest test/toolkits/test_search_functions.py -k "you" -v
```

Working tree is clean. No remote pushes performed.
