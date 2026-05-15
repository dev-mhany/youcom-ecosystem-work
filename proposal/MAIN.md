# You.com Ecosystem Engagement — Proposal

**Date:** 2026-05-15
**Version:** v1.0
**Prepared for:** You.com ecosystem / partnerships team
**Presenter:** Gitonga Miriam (Cooperation.org), Monday 2026-05-18, 10:00 AM PT

## Executive summary

We propose a multi-region, multi-language open-source engagement that adds the You.com Search and Research APIs to agent and research frameworks where they are not yet present. The engagement is anchored by two reference forks already built against verified You.com endpoints: `camel-ai/camel` (KAUST origin, Arabic-community fit) and `assafelovic/gpt-researcher` (deep-research narrative fit). Each fork mirrors the upstream LangChain integration's parity contract and adds locale parameters (`country`, `search_lang`) that the existing LangChain wrapper does not expose for the Search endpoint.

The team is global and multi-language. Muhammad Hany leads Arabic outreach (KAUST, AUC). The Japanese leg is structured as a translation-plus-influencer play around Sakana AI's `AI-Scientist-v2`, with npaka (note.com) as the outreach contact. The two completed forks are the demonstration; the engagement we want is a recurring cadence that keeps adding repos like these on a documented schedule.

This document refines the earlier pre-Monday brief drafted by Peter B.A. and is built on verified primary sources: every claim about an existing You.com integration links to the file or release that proves it, and every recommended target lists the exact integration entry-point file.

## Sections

1. Existing You.com OSS footprint
2. Proposed targets (uncovered)
3. Two reference forks (already built)
4. Multi-region engagement plan
5. How we'll execute

---

## 1. Existing You.com OSS footprint

The table below lists every You.com integration we verified upstream. Status
column: **First-class** = the framework ships a You.com-named class or
package; **Docs/MCP** = the framework ships a recipe pointing at You.com's
remote MCP server but no You.com-named code; **Official** = You.com or a
named partner publishes the integration. Every row links to the file or
release that proves it.

| Project | Status | Contributor(s) | Source of record |
|---|---|---|---|
| LangChain (Python) | First-class | [sam-h-bean](https://github.com/sam-h-bean), [scottnath](https://github.com/scottnath), [christeefy](https://github.com/christeefy) | [`utilities/you.py`](https://github.com/langchain-ai/langchain-community/blob/main/libs/community/langchain_community/utilities/you.py), [`retrievers/you.py`](https://github.com/langchain-ai/langchain-community/blob/main/libs/community/langchain_community/retrievers/you.py), [`tools/you/tool.py`](https://github.com/langchain-ai/langchain-community/blob/main/libs/community/langchain_community/tools/you/tool.py) |
| LlamaIndex | First-class | run-llama maintainers | [LlamaIndex `YouRetriever`](https://docs.llamaindex.ai/en/stable/api_reference/retrievers/you/) |
| DSPy | First-class (`YouRM`) | [christeefy](https://github.com/christeefy) | [`dspy/retrieve/you_rm.py`](https://github.com/stanfordnlp/dspy/blob/2.6.27/dspy/retrieve/you_rm.py) (last present on tag `2.6.27`; absent in DSPy 3.x — re-introduction is a candidate task) |
| CrewAI 1.14.4 | Docs/MCP | crewAI maintainers | [1.14.4 release](https://github.com/crewAIInc/crewAI/releases/tag/1.14.4), [`youai-search.mdx`](https://raw.githubusercontent.com/crewAIInc/crewAI/1.14.4/docs/en/tools/search-research/youai-search.mdx), [`youai-contents.mdx`](https://raw.githubusercontent.com/crewAIInc/crewAI/1.14.4/docs/en/tools/web-scraping/youai-contents.mdx) |
| Composio | First-class | Composio maintainers | [Composio YouSearch toolkit](https://docs.composio.dev/toolkits/yousearch) |
| Vercel AI SDK | Official | You.com + Vercel | [You.com docs — Vercel AI SDK](https://documentation.you.com/sdks/typescript-sdk) |
| n8n | Official | You.com + n8n | [You.com integrations index](https://you.com/docs/integrations) (specific n8n node URL needs re-verification — the previously documented credentials path 404s) |
| Zapier | Official | You.com + Zapier | [You.com Zapier integration](https://zapier.com/apps/youcom) |
| MCP server | Official (You.com) | You.com | [`@youdotcom-oss/mcp` on npm](https://www.npmjs.com/package/@youdotcom-oss/mcp); endpoint `https://api.you.com/mcp` |
| Anthropic MCP Registry | Official | You.com | [Anthropic MCP Registry — You.com entry](https://github.com/modelcontextprotocol/servers) |
| Agent Skills | Official (You.com) | You.com | [`youdotcom-oss/agent-skills`](https://github.com/youdotcom-oss/agent-skills) |

### Notes per row

**LangChain.** The wrapper, retriever, and tool live in `langchain-community`
(the path `langchain-ai/langchain` at `libs/community/...` is an old
location; the package was split out). The wrapper exposes
`endpoint_type="search"|"news"|"rag"|"snippet"` and a Pydantic v2 surface;
it is the parity contract every fork we ship targets. Detail in
`research/benchmark-langchain.md`. Notable gap on the Search endpoint: the
upstream wrapper exposes `country` but does **not** expose
`search_lang` / `language` (the locale knobs are News-only). Our forks fix
this for the Search endpoint.

**LlamaIndex / DSPy.** First-class wrappers exist; we did not deep-dive the
parity contract. Treat as covered for the call.

**CrewAI.** Use the accurate phrasing: CrewAI 1.14.4 (Apr 30, 2026) ships
You.com integration **documentation** — agents connect to
`api.you.com/mcp` via CrewAI's pre-existing generic MCP support. No
CrewAI-specific Python class for You.com was added; the docs even include
a ~50-line user-side schema patch for `you-contents` because the generic
MCP layer doesn't cleanly fit the schema. Detail in
`research/crewai-verification.md`. Do **not** call this a first-class
CrewAI integration on the call.

**Composio.** First-class YouSearch toolkit; covered.

**Vercel / n8n / Zapier.** Standard official integrations; covered.

**MCP server.** `@youdotcom-oss/mcp` is published on npm and
`https://api.you.com/mcp` is live with `you-search`, `you-research`, and
`you-contents` tools. This is the surface CrewAI's docs page consumes.

**Eval coverage.** The You.com team maintains an eval harness that
benchmarks any web-search provider; it covers the LangChain integration.
We will reference it generically as "the You.com team's eval harness." If
methodology comes up, the harness is led by Eddy Nassif (Senior Applied
Scientist). Detail in `research/eddy-investigation.md`.

## 2. Proposed targets (uncovered)

Targets are tiered by what we propose to ship in this engagement vs. the
next one. Star counts and last-active dates are point-in-time
(2026-05-15). "Entry point" is the exact file where a `search_you` /
`YouSearch` / equivalent should land — the forks in section 3 prove this
analysis with working code.

### Tier 1 — this engagement (two reference forks, already built)

| Repo | Stars | Lang | Region/community signal | Integration entry point | Why this fits |
|---|---|---|---|---|---|
| [`camel-ai/camel`](https://github.com/camel-ai/camel) | ~13k | Python | KAUST origin (Saudi Arabia); Arabic-community story | [`camel/toolkits/search_toolkit.py`](https://github.com/camel-ai/camel/blob/master/camel/toolkits/search_toolkit.py) — add `search_you()` method + register in `get_tools()` | Multi-engine `SearchToolkit` already supports 15 search backends including Brave / Serper / Tavily; You.com is a clean drop-in. Brave's `search_lang` accepts `'jp'` (not `'ja'`) and offers no Arabic country mapping — You.com is the cleanest win for `ar-SA` / `ar-AE` / `ja-JP` use cases. Detail in `research/camel-recon.md`. |
| [`assafelovic/gpt-researcher`](https://github.com/assafelovic/gpt-researcher) | ~16k | Python | Deep-research narrative; closest framework analog to You.com Research API | [`gpt_researcher/retrievers/`](https://github.com/assafelovic/gpt-researcher/tree/master/gpt_researcher/retrievers) — new `you/` package + `you_search.py` | Tavily-shape contract (`{href, body}` per result) is the de-facto standard; Serper-style locale precedent (`country`, `language`) is already in the codebase. You.com Research API is the natural counterpart to gpt-researcher's "deep" mode. Detail in `research/gpt-researcher-recon.md`. |

### Tier 2 — recommended next (priority order)

| Repo | Stars | Lang | Region/community signal | Integration entry point | Why this fits |
|---|---|---|---|---|---|
| [`SakanaAI/AI-Scientist-v2`](https://github.com/SakanaAI/AI-Scientist-v2) (+ [v1](https://github.com/SakanaAI/AI-Scientist) paired) | ~6.3k + ~14k | Python | Japan (Sakana AI, Tokyo); flagship JP agent project | [`ai_scientist/tools/base_tool.py`](https://github.com/SakanaAI/AI-Scientist-v2/blob/main/ai_scientist/tools/base_tool.py) — subclass `BaseTool`, register in `perform_ideation_temp_free.py` alongside `SemanticScholarSearchTool` | Clean abstract `BaseTool` (`name`, `description`, `parameters`, `use_tool(**kwargs)`); only existing search backend is Semantic Scholar — You.com adds web grounding. JP-flagship endorsement is the engagement value. Detail in `research/japanese-community.md`. |
| [`huggingface/smolagents`](https://github.com/huggingface/smolagents) | ~17k | Python | HF distribution = multilingual reach by default | `src/smolagents/default_tools.py` (web-search tool slot) | HF's distribution and the multilingual model audience put a single PR in front of a wide developer base. |
| [`agno-agi/agno`](https://github.com/agno-agi/agno) | ~30k | Python | Active maintainers, clean tools layer | `libs/agno/agno/tools/` (per-provider file pattern, e.g. `tavily.py`, `exa.py`) | Drop-in pattern matches camel-ai; high-traffic project. |
| [`pydantic/pydantic-ai`](https://github.com/pydantic/pydantic-ai) | ~10k | Python | Pydantic ecosystem; common-tools convention | `pydantic_ai/common_tools/` (one file per provider, e.g. `tavily.py`) | Clean common-tools pattern; a `you.py` follows the existing template directly. |
| [`QwenLM/Qwen-Agent`](https://github.com/QwenLM/Qwen-Agent) | ~9k | Python | China / Asia, Alibaba-backed | `qwen_agent/tools/web_search.py` | Asia regional coverage; complements the JP fork target. |
| [`LearningCircuit/local-deep-research`](https://github.com/LearningCircuit/local-deep-research) | ~3k | Python | Direct fit for the Research API surface | `src/local_deep_research/web_search_engines/engines/` | Direct Research-API match; smaller audience but high relevance. |

### Tier 3 — future / dependent on roadmap

| Repo | Stars | Lang | Region/community signal | Integration entry point |
|---|---|---|---|---|
| [`deepset-ai/haystack`](https://github.com/deepset-ai/haystack) | ~17k | Python | German enterprise audience (deepset, Berlin) | `haystack/components/websearch/` |
| [`microsoft/semantic-kernel`](https://github.com/microsoft/semantic-kernel) | ~22k | C#/Python | Microsoft enterprise; connector pattern | `python/semantic_kernel/connectors/search/` |
| [`letta-ai/letta`](https://github.com/letta-ai/letta) | ~15k | Python | Stateful agents (formerly MemGPT) | Tool plug-in surface |
| [`pfnet-research/plamo-examples`](https://github.com/pfnet-research/plamo-examples) | ~25 | Jupyter | Japan (PFN brand) — examples repo, not framework code | `examples/` notebook addition |

## 3. Two reference forks (already built)

Both forks were built off the parity contract in
`research/benchmark-langchain.md` (the upstream LangChain integration is
the quality benchmark). Both target the verified Search endpoint
`GET https://ydc-index.io/v1/search` with the response shape captured at
`research/fixtures/search-response.json`. Both expose `country` and
`search_lang` / `language` for the Search endpoint — locale parameters the
upstream LangChain wrapper does not expose for Search (LangChain restricts
those to the News endpoint).

### 3.1 `camel-fork` — `camel-ai/camel`

Working tree: `E:\youdotcom\workspace\camel-fork\`. Branch off
upstream `master` at `0d917c0f`. Build report:
`research/camel-build-report.md`.

Commits (in order; `git log 0d917c0f..HEAD --reverse`):

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

What it adds: a `search_you()` method on `SearchToolkit` (in
`camel/toolkits/search_toolkit.py`) following the dominant per-engine
pattern (plain `requests` + `@api_keys_required` + try/except returning
an error dict). Locale params: `country` (ISO-2: `US`, `JP`, `AE`, `SA`)
and `search_lang` (BCP 47: `en`, `ja`, `ar`). Registered via
`FunctionTool(self.search_you)` in
`get_tools()`; the class-level `@MCPServer()` decorator makes it
auto-available as an MCP tool too. Tests appended to
`test/toolkits/test_search_functions.py` covering success, locale
(`country='JP', search_lang='ja'` and `search_lang='ar'`), HTTP error,
and request-exception cases.

Multilingual gap closed: camel-ai's existing engines either lack locale
entirely (Google hard-codes `search_language="en"`), use non-standard
codes (Brave's `search_lang='jp'` instead of `'ja'`), or don't list
Arabic at all (Querit's `language_include` enumerates English / Japanese /
Korean / German / French / Spanish / Portuguese — no Arabic). You.com's
`country` and `search_lang` fields, exposed via `search_you()`, give
camel-ai its cleanest path to Arabic and to a correctly-coded Japanese
locale.

### 3.2 `gpt-researcher-fork` — `assafelovic/gpt-researcher`

Working tree: `E:\youdotcom\workspace\gpt-researcher-fork\`. Branch off
upstream `master` at `92bfc038`. Build report:
`research/gpt-researcher-build-report.md`.

Commits (in order; `git log 92bfc038..HEAD --reverse`):

| # | Hash | Subject |
|---|---|---|
| 1 | `296826ff` | Scaffold You.com retriever package |
| 2 | `d0044789` | Implement YouSearch core retrieval |
| 3 | `b531be50` | Add soft-fail on missing API key (Tavily pattern) |
| 4 | `8b9e208a` | Wire YouSearch into retrievers registry |
| 5 | `ca93f2aa` | Add country and language locale support |
| 6 | `9bec63bc` | Add HTTP error handling |
| 7 | `077b8c63` | Add unit tests for YouSearch |
| 8 | `a319d30a` | Document You.com retriever in search-engines guide |
| 9 | `0158732e` | Rename YOU_SAFE_SEARCH env var to YOU_LANGUAGE |

What it adds: a `YouSearch` class in
`gpt_researcher/retrievers/you/you_search.py` modeled on
`tavily_search.py`. Constructor mirrors Tavily exactly
(`__init__(self, query, headers=None, topic="general", query_domains=None)`)
plus optional `country` and `language` kwargs that fall back to
`YOU_COUNTRY` / `YOU_LANGUAGE` env vars — same shape as the Serper
retriever's locale precedent (`SERPER_REGION`, `SERPER_LANGUAGE`). The
`search(max_results=7)` method returns the framework-standard
`[{"href", "body", "title"}, ...]` shape; on exception it returns `[]`
to match the Tavily pattern. Soft-fail on missing `YOU_API_KEY` (warn +
return blank), with per-request override via
`headers.get("you_api_key")` — supports the multi-tenant
header-injection path used by `backend/server/server_utils.py`.

Wired into the retrievers registry: import added to
`gpt_researcher/retrievers/__init__.py`, `case "you":` added to the
factory `match` block in `gpt_researcher/actions/retriever.py`, and
`"you"` added to the defensive `VALID_RETRIEVERS` list in
`gpt_researcher/retrievers/utils.py` (the dynamic directory scan picks
it up automatically; the list is belt-and-suspenders). Users select via
`RETRIEVER=you` and can compose with others via `RETRIEVER=tavily,you,arxiv`.

## 4. Multi-region engagement plan

The two forks above are the first two artifacts. The structure that
follows is the durable shape: per region, name a lead, name an outreach
target, name the first repo. We start with what we can demonstrate
(Arabic, live this engagement) and stage the rest behind it.

### Arabic — LIVE on this engagement

- **Lead:** Muhammad Hany.
- **First repo:** `camel-ai/camel`. Done — see section 3.1. KAUST is the
  origin of camel-ai, which makes the Arabic-community story land
  naturally rather than as an afterthought.
- **Outreach targets:** KAUST (camel-ai's institutional home) and the
  American University in Cairo. Outreach templates are in
  `extra/`.
- **Locale verification status:** `country=SA`, `country=AE`,
  `country=EG` and `search_lang=ar` are accepted by the Search API per
  documentation; live verification was not budgeted for this pass (we
  spent both budgeted live calls on endpoint correctness — see
  `research/api-reference.md` section 5). The fork's unit tests cover
  the Arabic locale path; an integration smoke test against a real
  Arabic query is the first item we'd want a You.com API key to run.

### Japanese — NEXT engagement

- **Outreach lead:** npaka (布留川英一,
  [@npaka123](https://twitter.com/npaka123)) on note.com / X. He has
  already written explainers on Tavily Search API + LangChain; a
  "You.com Search API を試す" post from him is the single
  highest-yield piece of JP coverage available.
- **First repo:** [`SakanaAI/AI-Scientist-v2`](https://github.com/SakanaAI/AI-Scientist-v2)
  (paired with [v1](https://github.com/SakanaAI/AI-Scientist) for ~20k
  combined stars). Sakana's codebase is English-language despite the
  Tokyo HQ, the team posts in English, and the only existing search
  backend is Semantic Scholar — a `BaseTool` subclass adding You.com is
  ~150 lines.
- **Conference:** PyCon JP 2026 CFP is open at
  https://pretalx.com/pyconjp2026/cfp. Talk angle:
  "オープンソース日本語エージェントに本物のWeb検索を" (Real web
  search for open-source Japanese agents). We'd partner with a
  JP-native co-presenter rather than present in machine-translated
  Japanese.
- **JP technical content:** authored only via paid translation
  (~$200-400 per Qiita + Zenn cross-post). We do not write JP-language
  technical content directly. Detail and rationale in
  `research/japanese-community.md` section 3.

### Other regions — roadmap

These are sketched, not promised. Each row needs a confirmed
team-member lead before it goes on the next engagement's commitment
list.

| Region | Language | Candidate first repo | Lead status |
|---|---|---|---|
| LATAM | Spanish | TBD (smolagents Spanish examples; Hugging Face community channels) | TBD |
| SEA | Indonesian / Vietnamese | TBD (Qwen-Agent has natural Asia surface; local LLM communities to be mapped) | TBD |
| Africa | Swahili | TBD (Cooperation.org has a relevant network; lead to be named) | TBD |
| Germany | German | `deepset-ai/haystack` (Berlin enterprise audience) | TBD |
| China | Mandarin | `QwenLM/Qwen-Agent` (Alibaba) | TBD |

## 5. How we'll execute

This section is the operating contract we propose for the engagement. It
is deliberately concrete: cadence, asks, and reporting format.

### 5.1 Engagement structure

- **Weekly sync** with the You.com partnerships / DevRel point-of-contact.
  30 minutes, agenda-driven, async-first (notes shared 24h ahead).
- **Per-fork PR cadence:** scaffold → implementation → tests → docs in
  separate commits, mirroring the cadence visible in the camel-fork and
  gpt-researcher-fork commit logs (sections 3.1 / 3.2). Upstream PRs
  opened in English; locale-test fixtures included.
- **Eval against the You.com team's existing harness** (Eddy Nassif's
  work). Every fork ships with at minimum one locale-positive case
  (e.g. `country='JP', search_lang='ja'`) and one locale-negative case;
  if the eval harness is open to outside contributors, we add the new
  framework backends to it. If methodology comes up on Monday we're
  prepared to discuss; otherwise we credit "the You.com team" generically.

### 5.2 What we need from You.com

- **API keys** for the team — minimum: lead developer per region
  (Muhammad for Arabic; one per future region as leads are confirmed)
  plus one shared key for CI / integration smoke tests. The forks'
  unit tests don't need keys; the live integration smoke tests do.
- **A direct channel** with You.com DevRel for fast questions
  (Slack channel, Discord, or shared email thread — whichever
  matches their existing partnerships flow). Specifically: schema
  questions on `you-contents` (the CrewAI doc workaround in section 1
  shows this is non-trivial) and confirmation of `country=SA` / `AE` /
  `EG` plus `language=AR` behavior with Arabic queries.
- **Optional pre-PR review** of the next batch of target repos before
  we open upstream PRs. We'd rather route through You.com once and ship
  cleanly than have to renegotiate after a maintainer asks "did You.com
  ask for this?"

### 5.3 Cadence

- **Minimum:** 2 forks per month upstream-PR-ready. Camel-ai +
  gpt-researcher are the proof of cadence — both built in this engagement
  prep window.
- **Scaling:** more, contingent on team availability and on locked
  region leads (section 4). The Tier 2 list in section 2 is six repos
  deep, which gives us 3 months of runway at the minimum cadence
  before we'd need to pull from Tier 3.

### 5.4 Reporting

Monthly markdown report mirroring this document's style and structure.
Required sections per report:

- PRs opened (link, status, maintainer signal).
- Outreach evidence (template links, replies, scheduled meetings).
- Eval delta vs. previous month if a harness comparison is run.
- Next month's commitment list (repo, lead, target PR-open date).

Reports live in the same `Cooperation-org/projects` repo that hosts
this proposal so You.com partnerships can read them in one place.

---

## Sources & verification methodology

Every claim in this document is grounded in primary research saved to
`E:\youdotcom\workspace\youcom-ecosystem-work\research\`. The relevant files:

- `benchmark-langchain.md` — LangChain wrapper fetched 2026-05-15; parity contract
- `api-reference.md` — endpoints, auth, response shape verified against a live 200
- `camel-recon.md`, `gpt-researcher-recon.md` — fork target reconnaissance
- `camel-build-report.md`, `gpt-researcher-build-report.md` — what each fork actually shipped
- `eddy-investigation.md` — identification of Eddy Nassif as the eval-harness lead
- `crewai-verification.md` — CrewAI integration is Type B (generic MCP), not first-class
- `japanese-community.md` — JP fork target ranking and outreach plan
- `fixtures/search-response.json` — captured live 200 from `https://ydc-index.io/v1/search`
