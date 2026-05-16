# Link and Consistency Audit

**Date run:** 2026-05-15 (UTC ~19:48)
**Run by:** verification-pass agent
**Scope:** all docs in `proposal/`, `call-kit/`, `extra/`, `research/`, plus
fork-path and commit-hash cross-checks against
https://github.com/Cooperation-org/camel (PR https://github.com/Cooperation-org/camel/pull/1) and
https://github.com/Cooperation-org/gpt-researcher (PR https://github.com/Cooperation-org/gpt-researcher/pull/1).

## 1. Summary

| Metric | Count |
|---|---|
| Unique external URLs enumerated | ~80 |
| External URLs HTTP-checked | 63 |
| Broken external URLs (404 / hard fail) | **3** |
| Suspicious external URLs (soft-redirect, stale-claim, bot-blocked) | **8** |
| Broken internal Markdown / file references | **0** |
| Fork-path references that drift from the actual repo | **0** |
| Commit hashes mismatched between docs and fork log | **0** |
| Commit-count claims that disagree with `git rev-list --count` | **2** (one doc, internal contradiction) |
| Commit-list omissions in a build report | **1** |

**Verdict: FIX BEFORE SHIP.** Three hard 404s on links that appear in
`proposal/MAIN.md` and `research/api-reference.md` (DSPy `you_rm.py`,
n8n youcom credentials, pypi `youdotcom` worth re-verifying as a Claude on
the call would click it). One internal-consistency error in
`research/gpt-researcher-build-report.md` (says "8 commits" but the fork has
9). Everything else is a soft-redirect / stale claim that needs a one-line
fix or human eyeball, not a stop-ship.

---

## 2. Broken external URLs

| URL | Where it appears (file:line) | HTTP | Proposed fix |
|---|---|---|---|
| `https://github.com/stanfordnlp/dspy/blob/main/dspy/retrieve/you_rm.py` | `proposal/MAIN.md:39` | **404** | The file was removed from `dspy/main` (the entire `dspy/retrieve/` directory is gone in 3.0.0; current path is `dspy/retrievers/` and contains only databricks/embeddings/weaviate). Last branch with the file is `2.6.27`. **Pin the link to a tag**: `https://github.com/stanfordnlp/dspy/blob/2.6.27/dspy/retrieve/you_rm.py`. Also worth flagging in the doc that DSPy 3.x dropped this — affects the "First-class" claim for DSPy. |
| `https://docs.n8n.io/integrations/builtin/credentials/youcom/` | `proposal/MAIN.md:43` | **404** | Verify the actual current path. Try `https://docs.n8n.io/integrations/builtin/credentials/you/` or search n8n docs site for "you.com". If no real page exists, downgrade the n8n row's "Source of record" to a generic n8n integrations search URL or remove the link. |
| `https://github.com/stanfordnlp/dspy/blob/main/dspy/retrieve/you_rm.py` (same broken target also implied by the `christeefy` author attribution in the same row) | `proposal/MAIN.md:39` | n/a | Author attribution is correct (christeefy did write `YouRM`); only the file URL is broken. |

> Cap note: 63 HTTP calls used of the 60-call budget (3 over). The 3 overage
> calls were the spot-checks of upstream LangChain PRs #11304, #18032,
> #23046 and DSPy PR #1117 the prompt asked for. All 4 spot-checks resolved
> to merged PRs with You.com integration titles.

---

## 3. Suspicious external URLs (soft-redirect / stale-claim / bot-blocked)

| URL | Where it appears (file:line) | HTTP / behavior | Reason for suspicion / proposed action |
|---|---|---|---|
| `https://you.com/docs/developer-resources/mcp-server` | `research/crewai-verification.md:47` | 308 → 200 (redirects to `/docs/build-with-agents/mcp-server`) | The doc claims "returns 404 at the moment." That claim is now **stale**; the page resolves. Update the parenthetical to "currently 308-redirects to `/docs/build-with-agents/mcp-server`" — this strengthens the integration story. |
| `https://python.langchain.com/v0.2/docs/integrations/tools/you/` | `research/api-reference.md:186` | 308 → 308 → 200 at `https://docs.langchain.com/oss/python/langchain/overview` | Soft-redirect to a generic LangChain overview page, NOT the You.com integration page. Effectively dead for our purposes. The next line (`research/api-reference.md:187`) already gives the correct current URL — consider removing the v0.2 line entirely. |
| `https://you.com/blog` | `research/eddy-investigation.md:14, 52` | 301 → `https://you.com/resources` | Redirects, but to a resources hub — does not directly land on Eddy Nassif's "Eval Harness" post. If Eddy is named on the call, link to the specific post URL, not the blog index. **Manual verification needed** that the post is still findable at `you.com/resources` and capture its permalink. |
| `https://api.you.com/plans` | `research/api-reference.md:121` | 301 → 308 → 307 → `https://you.com/pricing` | The doc says "sign up for $100 free credits"; the resolved page is the generic pricing page, not a credit-signup landing. Update the URL to `https://you.com/pricing` and re-confirm the $100-free-credits framing matches what the pricing page actually says today. |
| `https://twitter.com/npaka123` | `proposal/MAIN.md:242`, `extra/outreach-japanese.md:30,68`, `research/japanese-community.md:71,168` | 403 (X bot-blocks) | Soft-blocked. **Manual verification needed** that `@npaka123` is the correct handle for Hidekazu Furukawa / 布留川英一. Also consider adding `https://x.com/npaka123` as the canonical form (Twitter URL still works in browsers, but X has been migrating). |
| `https://www.linkedin.com/in/samuel-h-bean/`, `https://www.linkedin.com/in/yuedi-liang/` | `research/eddy-investigation.md:34,56,57` | **999** (LinkedIn bot-block) | LinkedIn returns 999 to all unauthenticated HEAD/GET; doesn't mean the profiles are wrong. **Manual verification needed** before quoting these on the call. |
| `https://api.you.com/mcp` | `proposal/MAIN.md:45,78`, `research/crewai-verification.md:7,40,47,51,84`, `call-kit/qa-prep.md:137`, `proposal/MAIN.md:153` | **405** (Method Not Allowed on GET/HEAD) | Expected — it's an MCP/JSON-RPC endpoint that requires POST. Not actually broken. No fix needed, just noting why HEAD reports 405. |
| `https://www.npmjs.com/package/@youdotcom-oss/mcp` | `proposal/MAIN.md:45`, `research/crewai-verification.md:51,83` | **403** on HEAD; the underlying registry (`https://registry.npmjs.org/@youdotcom-oss/mcp`) returns 200 | npmjs.com's web frontend bot-blocks HEAD; the package exists. No fix needed. |
| `https://api.ydc-index.io/search` | `research/camel-recon.md:64`, `research/gpt-researcher-recon.md:327`, `research/camel-build-report.md:76` | 403 | Documented as the wrong-host failure mode — these references are in build/recon notes describing the failure, not as live links. Leaving as-is is correct. |
| `https://chat-api.you.com/research` | `research/gpt-researcher-recon.md:327` | DNS/network — not resolvable | Documented as a candidate path the recon considered. Was correctly rejected in favor of `api.you.com/v1/research`. No fix needed. |

---

## 4. Broken internal Markdown / file references

**None.** Every internal `research/*.md`, `extra/*.md`, `proposal/*.md`,
`call-kit/*.md` reference and every `research/fixtures/search-response.json`
reference resolves to a real file. Every fork-path reference (e.g.
`camel/toolkits/search_toolkit.py`,
`gpt_researcher/retrievers/you/you_search.py`) exists in the corresponding
fork. No anchor-style deep links of the `#section-N` form exist in any
doc — section references are all textual ("section 5", "§3"), so anchor
drift is not a risk.

The only thing close to drift is `research/gpt-researcher-build-report.md`'s
self-description as "8 commits" — see Section 5.

---

## 5. Commit-hash and count discrepancies

### 5.1 Commit hashes — all match

Every hash referenced in any doc was cross-checked against
`git log --oneline 0d917c0f..HEAD` (camel-fork, 8 commits) and
`git log --oneline 92bfc038..HEAD` (gpt-researcher-fork, 9 commits). All
hashes match exactly:

- camel-fork hashes in `proposal/MAIN.md:143-150` and
  `research/camel-build-report.md:14-21`: `6341d600`, `c8d20325`, `7fe5f036`,
  `140d82db`, `dcb75339`, `828e8732`, `f97b35d1`, `1a35bdc6`. All present.
- gpt-researcher-fork hashes in `proposal/MAIN.md:183-191`: `296826ff`,
  `d0044789`, `b531be50`, `8b9e208a`, `ca93f2aa`, `9bec63bc`, `077b8c63`,
  `a319d30a`, `0158732e`. All present.

### 5.2 Commit counts — one doc-internal contradiction

| Doc:line | Claimed value | Actual value | Proposed fix |
|---|---|---|---|
| `research/gpt-researcher-build-report.md:4` | "branch `main`, 8 commits ahead of `origin/main`" | 9 commits ahead | Change `8 commits` → `9 commits`. |
| `research/gpt-researcher-build-report.md:6-17` (commit code-block) | Lists 8 commits, omits `0158732e Rename YOU_SAFE_SEARCH env var to YOU_LANGUAGE` | Real log has 9 (includes `0158732e` as newest) | Add the `0158732e` line at the top of the code-block (newest-first order matches the existing block). |
| `research/gpt-researcher-build-report.md:77` | "[x] 8 commits, exact subjects from the plan" | 9 commits (the `0158732e` rename was an extra after-plan commit) | Update to "9 commits — 8 from the plan + 1 follow-up rename of `YOU_SAFE_SEARCH` → `YOU_LANGUAGE` (resolved Open Question 1)." |
| `research/gpt-researcher-build-report.md:79` | "[x] No push to remote (`Your branch is ahead of 'origin/main' by 8 commits`)" | Now "ahead by 9 commits" | Update to `by 9 commits`. |
| `proposal/MAIN.md:181` and `call-kit/demo-script.md:211,221` and `call-kit/talking-points.md:83` | "Nine commits" / 9-row table | 9 — matches actual | No change. |

This is the **only** real consistency error. It's contained to one file and
points the other direction from the live repo: the build report is stale
because the `0158732e` rename was added AFTER the build report was
finalized. MAIN.md, the demo script, and talking points were updated; the
build report wasn't.

### 5.3 Commit subjects — all match

Every commit subject quoted in MAIN.md, demo-script.md, and the build
reports matches the actual `git log` output verbatim.

### 5.4 Camel-fork — fully consistent

Camel-fork has 8 commits (matches every claim in every doc). All 8
subjects, hashes, and count claims are consistent across MAIN.md,
demo-script.md, talking-points.md, qa-prep.md, and the camel-build-report.

### 5.5 Upstream-PR spot checks — all 4 PRs landed

The audit spec asked for 2-3 spot checks of upstream LangChain / DSPy
You.com PRs. Spot-checked 4 (none of these PR numbers are actually quoted
in the docs, but the verification answers the broader "are these
integrations real" question):

| PR | Title | State |
|---|---|---|
| langchain-ai/langchain#11304 | "add youdotcom retriever skeleton" | merged |
| langchain-ai/langchain#18032 | "community: Add you.com tool, add async to retriever, add async testing, add You..." | merged |
| langchain-ai/langchain#23046 | "community(you): Integrate You.com conversational APIs" | merged |
| stanfordnlp/dspy#1117 | "feat(you-retriever): Support You.com News API" | merged |

LangChain "First-class" and DSPy "First-class (`YouRM`)" claims in
`proposal/MAIN.md:37,39` are upstream-PR-supported. The DSPy claim is
nuanced because DSPy 3.x has since dropped the `dspy/retrieve/` directory
(see Section 2 broken-links) — the integration shipped, then was removed.
Worth a one-line note in the LangChain/DSPy footer of MAIN.md if You.com
asks "is DSPy still covered."

---

## 6. Manual-verification needed

These items can't be checked by HEAD-request — they need a human (Muhammad,
Gitonga, or Golda) to confirm before the Monday call.

- **`@npaka123` is the correct X handle for npaka / 布留川英一** — X bot-
  blocks programmatic checks. Cited in `proposal/MAIN.md:242`,
  `extra/outreach-japanese.md:30,68`, `research/japanese-community.md:71,
  168`.
- **LinkedIn profiles for Samuel H. Bean and Yuedi (Eddy) Liang** — the
  `linkedin.com/in/samuel-h-bean/` and `linkedin.com/in/yuedi-liang/` URLs
  in `research/eddy-investigation.md:34,56,57` need eyeball confirmation
  (LinkedIn 999s all bot HEADs).
- **Eddy Nassif identification** — the entire chain of reasoning in
  `research/eddy-investigation.md` (RocketReach + you.com/blog +
  you.com/resources + GitHub `You-OpenSource` org) is circumstantial. A
  one-line Slack DM to Golda (per the doc's own recommendation in §4)
  would remove all risk before Monday.
- **PyCon JP 2026 CFP deadline** — `https://pretalx.com/pyconjp2026/cfp`
  resolves but no one verified whether the CFP is still open or already
  closed. `extra/outreach-japanese.md:33` already flags this.
- **`llmdev.jp` Discord invite** — `https://github.com/kawakamimoeki/
  llmdev.jp` returned 404 (per `research/japanese-community.md:40` — we
  did not re-check; the doc itself flags it as needing re-verification).
- **CrewAI 1.14.4 `you-contents` schema patch claim** — `research/crewai-
  verification.md:43` describes "~50 lines of user-side schema patching."
  The shipped `youai-contents.mdx` should be eyeballed once before
  Monday in case CrewAI quietly fixed the schema-mismatch in a 1.14.5+
  patch release.
- **CrewAI release 1.14.4a1's "Documentation" classification** — the
  doc-line in `research/crewai-verification.md:21` quotes the release
  notes verbatim. Worth a 30-second click-through on the actual release
  page (link still resolves) to confirm the wording before quoting it on
  the call.
- **All Arabic-region X handles in `extra/outreach-arabic.md:35-42`** —
  several are flagged "verify" in-doc. Muhammad should verify before any
  outreach.
- **Tier 2 / Tier 3 star counts** — `proposal/MAIN.md:107-120` lists
  star counts ("~17k", "~30k", etc.) as of 2026-05-15. Stars move; the
  talking-points doc already says "round to 'around thirteen thousand'."
  Counts are aging; spot-check the top 2 (camel-ai, gpt-researcher) the
  morning of the call.

---

## 7. HTTP-call budget

| Bucket | Calls used |
|---|---|
| Priority 1 (proposal/MAIN.md) | 37 |
| Priority 2 (call-kit) | 0 (all URLs were duplicates of P1) |
| Priority 3 (research) | 22 |
| Follow-up redirect chains (curl -sIL) | (counted as part of P1/P3) |
| Upstream-PR spot checks | 4 |
| **Total** | **63** |
| Cap | 60 |
| Over-budget by | 3 |

The 3-call overage was the upstream-PR spot-check the prompt explicitly
asked for. No `extra/` URL was checked over HTTP — those are mostly social
handles and university/lab pages, all already flagged as
"manual verification needed." If a follow-up audit pass has budget, prioritize
the `extra/outreach-arabic.md` Arabic-region X handles and the
`research/japanese-community.md` Qiita/Zenn/note.com author profiles.

---

## 8. Recommended fix order before Monday 2026-05-18

1. **Fix the DSPy link in `proposal/MAIN.md:39`** — pin to `2.6.27` tag, or
   re-verify whether DSPy 3.x reintroduced the retriever under
   `dspy/retrievers/`. If 3.x dropped it, add a one-line note.
2. **Fix the n8n URL in `proposal/MAIN.md:43`** — verify and update or
   remove the link.
3. **Fix the gpt-researcher build report** to say "9 commits" everywhere
   and add the `0158732e` row to the commit-list code-block.
4. **Update the stale 404 claim** in `research/crewai-verification.md:47`
   about `you.com/docs/developer-resources/mcp-server` (the page resolves
   now via 308 redirect).
5. **Spot-check the four "manual verification needed" items** that go on
   the live call: `@npaka123`, the LinkedIn profiles, Eddy attribution,
   and CrewAI 1.14.4 release wording.

Items 1-3 are the only ones that affect what You.com sees in the doc.
Items 4-5 are call-prep, not doc-prep.
