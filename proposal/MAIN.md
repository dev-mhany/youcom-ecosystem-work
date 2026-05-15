# You.com Ecosystem Engagement — Proposal

**Date:** 2026-05-15
**Version:** v0.1
**Prepared for:** You.com ecosystem / partnerships team
**Presenter:** Gitonga Miriam (Cooperation.org), Monday 2026-05-18, 10:00 AM PT

## Executive summary

We propose a multi-region, multi-language open-source engagement that adds the You.com Search and Research APIs to agent and research frameworks where they are not yet present. The engagement is anchored by two reference forks already built against verified You.com endpoints: `camel-ai/camel` (KAUST origin, Arabic-community fit) and `assafelovic/gpt-researcher` (deep-research narrative fit). Each fork mirrors the upstream LangChain integration's parity contract and adds locale parameters (`country`, `search_lang`) that the existing LangChain wrapper does not expose for the Search endpoint.

The team is global and multi-language. Muhammad Hany leads Arabic outreach (KAUST, AUC). The Japanese leg is structured as a translation-plus-influencer play around Sakana AI's `AI-Scientist-v2`, with npaka (note.com) as the natural amplifier. The pitch credibility is the two completed forks; the engagement we want is a recurring cadence that keeps adding repos like these on a documented schedule.

This document supersedes the earlier pre-Monday brief drafted by Peter B.A. It is built on verified primary sources: every claim about an existing You.com integration links to the file or release that proves it, and every recommended target lists the exact integration entry-point file.

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
| DSPy | First-class (`YouRM`) | [christeefy](https://github.com/christeefy) | [`dspy/retrieve/you_rm.py`](https://github.com/stanfordnlp/dspy/blob/main/dspy/retrieve/you_rm.py) |
| CrewAI 1.14.4 | Docs/MCP | crewAI maintainers | [1.14.4 release](https://github.com/crewAIInc/crewAI/releases/tag/1.14.4), [`youai-search.mdx`](https://raw.githubusercontent.com/crewAIInc/crewAI/1.14.4/docs/en/tools/search-research/youai-search.mdx), [`youai-contents.mdx`](https://raw.githubusercontent.com/crewAIInc/crewAI/1.14.4/docs/en/tools/web-scraping/youai-contents.mdx) |
| Composio | First-class | Composio maintainers | [Composio YouSearch toolkit](https://docs.composio.dev/toolkits/yousearch) |
| Vercel AI SDK | Official | You.com + Vercel | [You.com docs — Vercel AI SDK](https://documentation.you.com/sdks/typescript-sdk) |
| n8n | Official | You.com + n8n | [n8n You.com node](https://docs.n8n.io/integrations/builtin/credentials/youcom/) |
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
| [`SakanaAI/AI-Scientist-v2`](https://github.com/SakanaAI/AI-Scientist-v2) (+ [v1](https://github.com/SakanaAI/AI-Scientist) paired) | ~6.3k + ~14k | Python | Japan (Sakana AI, Tokyo); flagship JP agent project | [`ai_scientist/tools/base_tool.py`](https://github.com/SakanaAI/AI-Scientist-v2/blob/main/ai_scientist/tools/base_tool.py) — subclass `BaseTool`, register in `perform_ideation_temp_free.py` alongside `SemanticScholarSearchTool` | Clean abstract `BaseTool` (`name`, `description`, `parameters`, `use_tool(**kwargs)`); only existing search backend is Semantic Scholar — You.com adds web grounding. JP-flagship endorsement is the engagement leverage. Detail in `research/japanese-community.md`. |
| [`huggingface/smolagents`](https://github.com/huggingface/smolagents) | ~17k | Python | HF distribution = multilingual reach by default | `src/smolagents/default_tools.py` (web-search tool slot) | HF's distribution and the multilingual model audience make this a cheap force-multiplier. |
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

*To be filled.*

## 4. Multi-region engagement plan

*To be filled.*

## 5. How we'll execute

*To be filled.*

---

## Sources & verification methodology

Every claim in this document is grounded in primary research saved to
`E:\youdotcom\workspace\youcom-ecosystem-work\research\`. The relevant files:

- `benchmark-langchain.md` — LangChain wrapper fetched 2026-05-15; parity contract
- `api-reference.md` — endpoints, auth, response shape verified against a live 200
- `camel-recon.md`, `gpt-researcher-recon.md` — fork target reconnaissance
- `eddy-investigation.md` — identification of Eddy Nassif as the eval-harness lead
- `crewai-verification.md` — CrewAI integration is Type B (generic MCP), not first-class
- `japanese-community.md` — JP fork target ranking and outreach plan
- `fixtures/search-response.json` — captured live 200 from `https://ydc-index.io/v1/search`
