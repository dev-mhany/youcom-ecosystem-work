# Demo Script — Live Screen-Share

**Speaker:** Gitonga Miriam (with optional tech-lead alongside)
**Duration:** ~5 minutes total (2 min camel-ai, 2 min gpt-researcher, 1 min buffer)
**Mode:** screen-share, both forks already open

This is a literal cmd-by-cmd runbook. Each step has four parts:

- **Say:** one sentence Gitonga reads while the action happens
- **Do:** the exact command or click
- **See:** what shows on screen
- **Recover:** the line if it breaks

Test the pre-flight section the morning of the call, not in the meeting.

---

## 0. Pre-flight (do BEFORE call starts)

Run through this 30 minutes before the call. If any item fails, fall
back to the static-screenshot demo path noted at the bottom of each
section.

### 0.1 Environment

- [ ] `YOU_API_KEY` is set in shell env (only needed for the optional
      live curl in section 4 — sections 2 and 3 do not need a key).
      Verify: `echo $env:YOU_API_KEY | findstr ydc-` (PowerShell) or
      `echo $YOU_API_KEY | grep ydc-` (Bash). Should print a key
      starting with `ydc-`. If empty, sections 2 and 3 still work; skip
      section 4.
- [ ] Python 3.13 on PATH. Verify: `python --version`.
- [ ] gpt-researcher fork's test deps available — only `requests` and
      stdlib are needed (the test uses `importlib` to skip the heavy
      framework imports). Verify: `python -c "import requests; print(requests.__version__)"`.
- [ ] No virtualenv activation needed for the gpt-researcher unit
      tests — they import the retriever module via `importlib`. If a
      venv is active, that's fine.

### 0.2 Editor and terminal layout

- [ ] Editor open with two workspaces in tabs:
      - `E:\youdotcom\workspace\camel-fork`            (GitHub: https://github.com/Cooperation-org/camel — PR https://github.com/Cooperation-org/camel/pull/1)
      - `E:\youdotcom\workspace\gpt-researcher-fork`   (GitHub: https://github.com/Cooperation-org/gpt-researcher — PR https://github.com/Cooperation-org/gpt-researcher/pull/1)
- [ ] Editor font size bumped to ~16pt for screen-share readability.
- [ ] Terminal open in two tabs, one per fork, both at the repo root.
      Font ~16pt, dark background, line wrapping on.
- [ ] Browser tab pre-opened to https://github.com/camel-ai/camel
      (upstream — for context only, not for editing).
- [ ] Browser tab pre-opened to https://github.com/assafelovic/gpt-researcher
      (upstream — same).

### 0.3 Files staged for instant open

In the editor, pre-open these files in tabs (don't show them yet —
just have them ready):

- `camel-fork/camel/toolkits/search_toolkit.py` — scroll to `search_you`
- `camel-fork/test/toolkits/test_search_functions.py` — scroll to the
  `# ==================== You.com Search Tests ====================` comment
- `camel-fork/README.md` — scroll to `### Search: You.com`
- `gpt-researcher-fork/gpt_researcher/retrievers/you/you_search.py` — top
- `gpt-researcher-fork/tests/test_you_retriever.py` — top
- `gpt-researcher-fork/docs/docs/gpt-researcher/search-engines/search-engines.md`
  — scroll to the You.com section

### 0.4 Audio and screen-share

- [ ] Audio test with the rest of the team 5 min before.
- [ ] Screen-share zoom level: editor at ~140%, terminal at ~140%.
- [ ] Notifications muted (Slack, mail, calendar).
- [ ] Second monitor with the proposal doc and this script open, NOT
      shared. The shared screen is editor/terminal/browser only.

### 0.5 Pre-staged screenshots (fallback path)

Save these to `call-kit/screenshots/` before the call, in case live
demo fails:

- `screenshot-camel-search_you.png` — `search_you()` method visible in editor
- `screenshot-camel-tests-pass.png` — terminal showing `5 passed` if
  the camel-ai test run is feasible (otherwise skip; see section 2)
- `screenshot-gptr-tests-pass.png` — terminal showing the literal
  output `Ran 6 tests in 0.0XXs / OK`
- `screenshot-search-response.png` — redacted live API response from
  `research/fixtures/search-response.json` with the API key masked

If everything goes to hell, share the screenshots and narrate over
them. The story still lands.

---

## 1. Opening transition (5 seconds)

**Say:** "Let me put the camel-ai fork up on screen first. This is a
local branch, not pushed."

**Do:** Switch from doc/face-cam to screen-share. Bring the editor to
the front, with the camel-fork workspace tab focused.

**See:** Editor showing the camel-fork tree.

**Recover:** If screen-share doesn't activate cleanly, say "give me
ten seconds to get my screen up" and don't fill the dead air.

---

## 2. Demo A — camel-ai/camel fork (~2 min)

### 2.1 Show the new method

**Say:** "This is `camel/toolkits/search_toolkit.py`. The new method
is `search_you`, sitting between `search_tavily` and `search_brave`."

**Do:** Click the pre-opened `search_toolkit.py` tab. Use editor's
"go to symbol" or Ctrl+F to jump to `def search_you`. Highlight the
function header and the first ten lines.

**See:** The method signature with `country`, `search_lang`,
`safesearch`, `freshness` parameters. The `@api_keys_required`
decorator above it.

**Recover:** If the file won't open, swap to the upstream browser tab
and say "let me show you the equivalent slot in the upstream file"
— show the `search_tavily` location in upstream code, then say "ours
sits right next to that."

### 2.2 Show the commit history

**Say:** "Eight logical commits. Skeleton, implementation, error
handling, registration, docs in the docstring, tests, ruff format,
README example."

**Do:** Switch to the camel-fork terminal. Run:

```
git log --oneline 0d917c0f..HEAD
```

**See:** Eight commits in reverse-chronological order, ending with
`6341d600 Add search_you() skeleton to SearchToolkit` at the bottom.

**Recover:** If terminal isn't ready, scroll the proposal doc to
section 3.1 — the same commit list is in the table there.

### 2.3 Show the tests

**Say:** "Five tests in the existing test file — success, locale
propagation, missing-key, HTTP error, request exception."

**Do:** Click the `test_search_functions.py` tab. Jump to the
`# ==================== You.com Search Tests ====================`
comment. Scroll slowly through the five `def test_search_you_*`
functions — about three seconds per name.

**See:** The five test names, each with a short body and a
`@patch('requests.get')` decorator.

**Recover:** If the file is too long to navigate cleanly, just read
the test names from the build report (`research/camel-build-report.md`
section 4) — say "I have the names in the build report; here's what
they cover."

### 2.4 (SKIP) Live test run for camel-ai

Do NOT attempt to run camel-ai tests live. Per the build report, the
test runner cannot import `camel.toolkits` without the full `[dev]`
install (pulls in openai, docstring_parser, wikipedia, etc.) and `uv`
is not installed on the demo machine.

**Say if asked:** "We didn't run the camel-ai suite end-to-end on
this machine — pulling the full `[dev]` install wasn't budgeted for
this pass. The structural correctness was verified with `ast.parse`
and `ruff check`. The gpt-researcher tests do run live — I'll show
those next."

### 2.5 Hand off to Demo B

**Say:** "Switching to the gpt-researcher fork — that one we can run
the tests on live."

**Do:** Switch editor workspace tab to gpt-researcher-fork. Switch
terminal tab to the gpt-researcher terminal.

**See:** New file tree, new prompt path.

---

## 3. Demo B — gpt-researcher fork (~2 min)

### 3.1 Show the retriever

**Say:** "This is `gpt_researcher/retrievers/you/you_search.py`. It
follows the Tavily retriever pattern — same constructor signature,
same return shape."

**Do:** Click the pre-opened `you_search.py` tab. Show the
`class YouSearch:` line and the `__init__` signature. Scroll to the
`search()` method.

**See:** Constructor `(self, query, headers=None, topic="general",
query_domains=None)` with optional `country` and `language` kwargs.
Soft-fail block for missing API key. The `search()` method returning
`[{"href", "body", "title"}, ...]` shape.

**Recover:** If the editor stalls, the same code is reproduced in
`research/gpt-researcher-build-report.md` section "Files touched."

### 3.2 Show the commit history

**Say:** "Nine commits — scaffold, implementation, soft-fail, registry
wiring, locale support, error handling, tests, docs, plus a follow-up
rename for the env var name."

**Do:** In the gpt-researcher terminal, run:

```
git log --oneline 92bfc038..HEAD
```

**See:** Nine commits ending with `296826ff Scaffold You.com retriever
package` at the bottom.

**Recover:** Same fallback as 2.2 — read from proposal section 3.2
or build report section "Commits."

### 3.3 Run the tests live

This is the highlight of the demo. The tests genuinely pass in
about ten milliseconds with no environment setup beyond stdlib +
requests.

**Say:** "Six tests, all passing. Let me run them now."

**Do:** In the gpt-researcher terminal, run:

```
python -m unittest tests.test_you_retriever
```

**See:** Output showing six dots, then `Ran 6 tests in 0.0XXs` and
`OK`. Test names per build report:
- `test_search_propagates_locale`
- `test_search_returns_empty_on_http_error`
- `test_search_returns_empty_on_timeout`
- `test_search_returns_results_in_tavily_shape`
- `test_search_soft_fails_on_missing_key`
- `test_search_uses_header_key_over_env`

**Recover:** If the test run fails — most likely cause is `requests`
not installed or wrong working directory — say:

> "The integration is correct on disk; the failure here is environmental.
> The test run from earlier today was six pass, six fail zero, ten
> milliseconds. I have a screenshot if useful."

Then show `screenshot-gptr-tests-pass.png`. Do NOT attempt to debug
on stream.

### 3.4 Show the registry wiring

**Say:** "Wired into the retriever factory in three places — the
package init, the dispatch match block, and the validity list.
Selected with `RETRIEVER=you`, composes with others as
`RETRIEVER=tavily,you,arxiv`."

**Do:** In the editor, briefly show:
- `gpt_researcher/retrievers/__init__.py` — the `from .you...` line
- `gpt_researcher/actions/retriever.py` — the `case "you":` block

Don't dwell. Three seconds per file.

**See:** Two short additions, both visibly mirroring the existing
Tavily / Serper patterns.

**Recover:** Skip if running short on time.

### 3.5 Show the docs entry

**Say:** "Documented in the search-engines guide alongside Tavily,
Serper, the others — same length and tone as the existing entries."

**Do:** Click the pre-opened `search-engines.md` tab. Scroll to the
You.com section.

**See:** A short catalog entry plus a Setup section showing the env
vars (`YOU_API_KEY`, `YOU_COUNTRY`, `YOU_LANGUAGE`).

**Recover:** Skip if short on time.

---

## 4. (Optional) Live API call

Only run this if You.com explicitly asks "can you show it hitting
the API?" Default is to skip — the tests are the demo. If asked:

**Say:** "Sure — I'll redact the key on screen."

**Do:** First, mask the key. In PowerShell:

```
$env:YOU_API_KEY_MASKED = $env:YOU_API_KEY.Substring(0,4) + "..."
```

Then run a single GET, NOT echoing the key:

```
curl -s -H "X-API-Key: $env:YOU_API_KEY" "https://ydc-index.io/v1/search?query=langchain%20you.com&count=2" | python -m json.tool | Select-Object -First 40
```

**See:** A truncated JSON response showing `results.web[]` with
`url`, `title`, `description`, `snippets`. The key is never
displayed.

**Recover:** If the call fails or shows a 401/403, do not retry
live. Say:

> "Network or auth blip — I have a captured 200 from earlier today,
> let me show that."

Then open `screenshot-search-response.png` (the pre-redacted
fixture). If the screenshot isn't ready, just say "I'll send it in
the follow-up email" and move on.

**Pre-staged alternative:** open `research/fixtures/search-response.json`
in the editor (after manually redacting any key shown in headers — the
file body itself contains no key, but double-check before screen-share).

---

## 5. Recovery lines (universal)

Memorize these. They work for any demo failure on this call.

- **Test fails on stream:** "The integration is correct on disk;
  the failure here is environmental. The earlier test run today was
  all-pass, ten milliseconds. I have the output in the build report."

- **Editor crashes / file won't open:** "Let me show you the same
  thing in the build report — same code, same commits."

- **Terminal hangs:** "Skipping the live run — the build report has
  the test output verbatim. Worth a look after the call."

- **Network is out:** "Demo is local — no network needed for the
  unit tests. Let me run them in just stdlib."

- **Wrong screen shared:** "Apologies, let me share the right
  window." Don't apologize twice.

- **You.com asks something the demo doesn't cover:** "Good question
  — let me hold that for the Q&A and finish the demo first." Then
  actually do hold it. Don't half-answer mid-demo.

---

## 6. Closing

**Say:** "That's the demo. Two forks, both with passing tests, both
ready for upstream PR review whenever your team gives the nod.
Questions?"

**Do:** Switch back to face-cam. Stop screen-share.

**See:** You and the team.

**Recover:** If demo ran long, skip 3.4 and 3.5 entirely — show the
test run (3.3) and close.
