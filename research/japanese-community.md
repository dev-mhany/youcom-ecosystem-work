# Japanese Open-Source AI/Agent Community — Fork-Target Recon

Goal: identify Python-or-Python-friendly repos rooted in the Japanese AI community where a clean You.com Search API integration would land, plus the channels we'd use for outreach. Counterpart to the Arabic angle (camel-ai/camel via KAUST).

---

## 1. Top fork candidates (ranked)

### #1 — SakanaAI/AI-Scientist-v2  (PRIMARY TARGET)
- URL: https://github.com/SakanaAI/AI-Scientist-v2
- Stars: ~6.3k  |  Forks: 848  |  License: AI Scientist Source Code License (RAIL derivative)
- Last commit: Dec 19, 2025 (active; v0.x cadence)
- Language: Python
- **Integration entry point:** `ai_scientist/tools/` — there is a clean `base_tool.py` abstract class (`name`, `description`, `parameters`, `use_tool(**kwargs)`). The only existing search backend is `ai_scientist/tools/semantic_scholar.py`. Adding `ai_scientist/tools/youcom.py` that subclasses `BaseTool` and registering it in `ai_scientist/perform_ideation_temp_free.py` (alongside the existing `SemanticScholarSearchTool` instantiation) is a ~150-line PR.
- **Why it fits:** Sakana AI is the most internationally visible Japanese AI lab in 2025-2026 (Lion-tier press, English-first communications). AI-Scientist already does literature search for ideation/citation; You.com would slot in as a web-grounding option that complements Semantic Scholar's academic-only scope. No existing You.com or `ydc-index` references in the repo.
- **Community signal:** Headline project — "AI Scientist" is the closest Japanese analog to gpt-researcher, repeatedly featured on Hacker News, ByteByteGo's "Top AI repos 2026," and aiagentindex.mit.edu.

### #2 — SakanaAI/AI-Scientist (v1)
- URL: https://github.com/SakanaAI/AI-Scientist
- Stars: ~14k  |  License: same RAIL derivative
- Last commit: Dec 19, 2025
- **Integration entry point:** Same architecture as v2 but older — Semantic Scholar + OpenAlex (no API key). The literature-search module is optional/swappable. We can either ship one PR to v1 and v2 simultaneously, or use v1 as the pitch piece (more stars, more outreach value) and v2 as the engineering reference.
- Treat v1 + v2 as a **paired drop** in the pitch.

### #3 — pfnet-research/plamo-examples
- URL: https://github.com/pfnet-research/plamo-examples
- Stars: ~25  |  Language: Jupyter Notebook  |  License: MIT-style (PFN OSS)
- Last commit: 2025 (low cadence but PFN-blessed)
- **Integration entry point:** Add a notebook `examples/web_search_youcom.ipynb` showing PLaMo + LangChain + You.com Search Retriever (RAG pattern). This repo already demos function calling and LangChain wiring — a You.com retriever fits the existing pedagogical pattern.
- **Why it fits:** PFN is Japan's most respected ML/research company (Optuna, CuPy, PLaMo). Even a low-star "examples" repo carries the PFN brand. PLaMo Prime is a paid API now, so a working web-search example is genuinely useful to PFN's customers.
- **Community signal:** Lower stars than Sakana, but the PFN brand opens doors at JP enterprises (Toyota, JR, etc.). Long-term ecosystem play.

### #4 — llm-jp/awesome-japanese-llm  (positioning play, not a code fork)
- URL: https://github.com/llm-jp/awesome-japanese-llm
- Stars: ~1.4k  |  Last commit: May 10, 2026  |  Language: Markdown
- **Integration entry point:** N/A (it's a curated list run by NII / LLM-jp consortium). Strategy: get You.com listed in the "Tools / Search" or "Agentic capability" section once we ship one of the code forks above. PR template + Japanese title line is straightforward.
- **Why it fits:** This is *the* canonical Japanese-LLM index, maintained by the National Institute of Informatics consortium. Listing here is high-leverage discovery for every JP LLM developer.

### #5 — kawakamimoeki/llmdev.jp
- URL: https://github.com/kawakamimoeki/llmdev.jp
- Note: Returned 404 on direct fetch — repo may have been moved to a private/community URL. Treat as a **community channel** (see §2) rather than a fork target until we re-verify. The GitHub URL was indexed as recently as the last few weeks, so it likely just renamed.

### Considered but deprioritized
- **SakanaAI/treequest** (535 stars, Python, Feb 2026): tree-search library, no retrieval surface. Could host a "RAG-augmented MCTS" demo but it's a stretch.
- **CyberAgentAILab/*** (TANGO, cmaes, layout-dm): all CV/optimization, no LLM-agent surface.
- **stockmarkteam/***: mostly book-companion notebooks; LLM repos are stale (last meaningful update 2024).
- **rinna/rinnakk** GitHub org: shows "no public repositories" — they ship via Hugging Face only. Outreach yes, fork no.
- **cl-tohoku, ku-nlp**: classical NLP labs (BERT-japanese, jumanpp, kwja). Strong brand, but no agent/retrieval surface that maps to You.com.
- **stockmark**, **ELYZA**: model weights only, no agent framework to fork.

---

## 2. Community channel map

### Qiita (qiita.com)
- Top tags to monitor: `#AIエージェント`, `#LLM`, `#RAG`, `#LangChain`, `#OpenAI`, `#生成AI`
- Key writers in the agent space:
  - `aokikenichi` — annual "生成AI・LLM必須リンク集" roundups (the JP equivalent of awesome-lists, very widely shared).
  - `Yasushi-Mo` — "LLM と AI Agent の役割と関係性を整理する" type explainers (high views).
  - `kenken38` — multi-agent architectures (recent self-evolving agent pieces).
  - `tikaranimaru` — model-comparison roundups.
- Outreach pattern: write/translate a short JP article ("You.com Search API を AI Scientist に組み込んでみた"), tag aggressively, link the PR.

### Zenn (zenn.dev)
- Same author overlap as Qiita; Zenn skews more engineer/long-form.
- Notable writers: `ryuhat` (AI実践ガイド2026 book), `kewa8579` (small-LLM JP rankings), `zephel01` (cross-platform writing tooling — interesting because it shows how JP devs syndicate to note + Qiita + Zenn).
- Zenn supports a "Books" format — a short bilingual book "Production-grade web search for Japanese LLM agents" would be high-credibility.

### note.com
- The most influential JP AI-dev personal-publishing platform.
- **npaka (布留川英一 / @npaka123)** — single most-followed JP LangChain/agent voice. Has already written explainers on Tavily Search API integration with LangChain (https://note.com/npaka/n/n9fe8a607c56e). A "You.com Search API を試す" post from him would be the single highest-leverage piece of JP coverage we could get. Reaching out via X reply is the standard channel.
- **karaage (@karaage0703)** — broader maker/AI audience, less LangChain-centric but huge reach.

### Discord
- **llmdev.jp** — kawakamimoeki's Japanese LLM-developer community (channels for Models, Vector DBs, LangChain, LLM app UX). Direct-developer audience; the right place to drop a "You.com 日本語検索 try it" post. Entry: link from https://github.com/kawakamimoeki/llmdev.jp (re-verify URL).
- **AI声づくり研究会** (DISBOARD: 1094999323365875773) — voice/multimodal heavy, secondary.
- **Stable Diffusion JP** servers — image/video heavy, not our audience.

### X / Twitter
- Must-follow / outreach handles: `@npaka123`, `@karaage0703`, `@SakanaAILabs`, `@PreferredNet`, `@llm_jp` (LLM-jp consortium), `@CyberAgent_AI`, `@elyza_ai`, `@stockmark_inc`.
- The JP LLM dev conversation lives on X far more than on Mastodon/Bluesky.

### Conferences / events
- **PyCon JP 2026** — CFP open at https://pretalx.com/pyconjp2026/cfp. A talk titled "オープンソース日本語エージェントに本物のWeb検索を" (Real web search for open-source Japanese agents) is a credible CFP angle if we partner with a JP-native speaker.
- **LLM 勉強会 / LLM-jp seminars** — https://llm-jp.nii.ac.jp/en/home-en/ — academic-industry seminar series at NII. Sponsorship/speaker slot is the formal path.
- **Sansan/Mercari/PFN tech talks** — the big JP tech companies run regular open meetups; smaller scope but warmer audience.

### University labs (long-term)
- **Tohoku NLP (cl-tohoku)** — https://github.com/cl-tohoku — Suzuki/Inui lab, BERT-japanese authors.
- **Kyoto University Language Media Processing Lab (ku-nlp)** — https://github.com/ku-nlp — Kurohashi lab, JUMAN++/KWJA authors.
- **U-Tokyo Miyao lab** — https://mynlp.is.s.u-tokyo.ac.jp/en/projects.
- These are not direct You.com prospects, but co-authoring a benchmark paper on "search-grounded JP QA" would create durable academic credibility.

---

## 3. Outreach approach (realistic, given weak Japanese)

Lead with **Sakana AI** because (a) the codebase is fully English-language despite the Tokyo HQ, (b) AI-Scientist v1+v2 is internationally famous, and (c) the team posts on X in English. The PR can be opened in English and will get reviewed in English. That's the single demonstrable artifact.

Once that PR is open, the Japanese-language leg is a **translation + influencer** play, not a from-scratch authoring play. Concrete sequence:

1. Land the AI-Scientist-v2 PR (+ optional v1 mirror). English-only.
2. Get a PFN engineer to merge a `plamo-examples` notebook (English README, JP-friendly comments — PFN engineers all read English).
3. Pay a JP technical translator (~$200-400) to render a 1500-word Qiita + Zenn cross-post under one team-member's account. Topic: "Sakana AI AI-Scientist + You.com Search API で本物の Web 検索を." Cross-link from EN blog.
4. Send a courtesy DM to `@npaka123` on X with the JP article + PR link. He posts about new search/RAG APIs habitually; no language barrier needed for the DM itself (he reads English fine), and the linked artifact is in JP.
5. Drop the same article into the llmdev.jp Discord and the LLM-jp Slack/Discord (verify channels).
6. Open a `awesome-japanese-llm` PR adding You.com under Tools/Search.
7. Submit a PyCon JP 2026 CFP — if we don't have a JP-native speaker, partner with whoever wrote the translation in step 3 as co-presenter.

What we should **not** try: writing JP technical content ourselves, replying to JP X threads in machine-translated Japanese, cold-emailing JP universities. Those will read as low-effort to JP devs and damage credibility.

---

## 4. Already-covered note

Verified absence of You.com / `ydc-index` integration in:
- SakanaAI/AI-Scientist (GitHub code search returned 0 matches)
- SakanaAI/AI-Scientist-v2 (only Semantic Scholar tool present in `ai_scientist/tools/`)
- pfnet-research/plamo-examples (only LangChain/OpenAI demos)
- llm-jp/awesome-japanese-llm (no entry under tools/search)

No fork-target candidate above is "already covered." Safe to claim.

LangChain itself ships a `YouSearchAPIWrapper` upstream, so the You.com/LangChain edge is well-known to JP devs who use LangChain — but that's a generic upstream integration, not a Japanese-rooted-project integration. Our angle (Japanese-flagship-project endorsement) is still uncovered.

---

## 5. Sources

- https://github.com/SakanaAI
- https://github.com/orgs/SakanaAI/repositories
- https://github.com/SakanaAI/AI-Scientist
- https://github.com/SakanaAI/AI-Scientist-v2
- https://github.com/SakanaAI/AI-Scientist-v2/tree/main/ai_scientist/tools
- https://github.com/SakanaAI/AI-Scientist-v2/blob/main/ai_scientist/perform_ideation_temp_free.py
- https://github.com/SakanaAI/AI-Scientist-v2/blob/main/ai_scientist/tools/base_tool.py
- https://github.com/SakanaAI/treequest
- https://sakana.ai/ai-scientist/
- https://aiagentindex.mit.edu/the-ai-scientist/
- https://deepwiki.com/SakanaAI/AI-Scientist-v2
- https://github.com/pfnet
- https://github.com/pfnet-research
- https://github.com/pfnet-research/plamo-examples
- https://www.preferred.jp/en/news/pr20230928 (PLaMo-13B release)
- https://tech.preferred.jp/en/blog/plamo-prime-release-feature-improvement/
- https://github.com/llm-jp
- https://github.com/llm-jp/awesome-japanese-llm
- https://llm-jp.github.io/awesome-japanese-llm/en/
- https://llm-jp.nii.ac.jp/en/home-en/
- https://github.com/cl-tohoku
- https://github.com/ku-nlp
- https://mynlp.is.s.u-tokyo.ac.jp/en/projects
- https://github.com/cyberagent
- https://github.com/CyberAgentAILab
- https://github.com/stockmarkteam
- https://huggingface.co/elyza/Llama-3-ELYZA-JP-8B
- https://huggingface.co/rinna
- https://github.com/rinnakk
- https://github.com/kawakamimoeki/llmdev.jp
- https://qiita.com/aokikenichi/items/fca5a1898cc2a9c961f6 (Qiita roundup)
- https://qiita.com/Yasushi-Mo/items/5bdaee4d9294cfb28a77 (LLM vs AI Agent)
- https://qiita.com/agdexai/items/650f3183902c0c7c5635 (Top 10 OSS Agent Frameworks 2026)
- https://qiita.com/masterpiecehack/items/25022951492d6ff9f274 (JP AI community list)
- https://zenn.dev/ryuhat/books/ai-practical-guide-2026
- https://note.com/npaka
- https://note.com/npaka/n/nb8bf95253033 (autonomous AI agent roundup)
- https://note.com/npaka/n/n9fe8a607c56e (Tavily Search API + LangChain — direct precedent)
- https://twitter.com/npaka123
- https://twitter.com/karaage0703
- https://disboard.org/server/1094999323365875773 (AI声づくり研究会)
- https://pretalx.com/pyconjp2026/cfp
- https://global.rakuten.com/corp/news/press/2025/1218_01.html (GENIAC / Rakuten AI 3.0 context)
