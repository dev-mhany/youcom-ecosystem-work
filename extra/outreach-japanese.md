# Japanese-Region Outreach Plan

NEXT engagement after Arabic. Condensed from `research/japanese-community.md`. Honest about the team's Japanese-language constraints.

---

## 1. Why this region matters

Sakana AI is the most internationally-visible Japanese AI org of 2025–2026 and operates in English by default — which makes them an unusually reachable first target. The PFN (Preferred Networks) ecosystem ships LLM research with English-readable code and is the most respected ML/research brand in JP enterprise. The actual JP developer audience lives on Qiita, Zenn, and X — not on Reddit or Hacker News, which means the standard US outreach playbook misses them entirely. Japanese queries are underserved by US-default search APIs in much the same way Arabic queries are; locale params (`country="JP"`, `search_lang="ja"`) are a demonstrable, copy-paste-able win.

---

## 2. Top fork targets

Condensed from the recon report. Full detail in `research/japanese-community.md` §1.

| # | Repo | Stars | Entry point | Why |
|---|------|------:|-------------|-----|
| 1 | **SakanaAI/AI-Scientist-v2** | ~6.3k | `ai_scientist/tools/youcom.py` (subclass `BaseTool`) + register in `ai_scientist/perform_ideation_temp_free.py` | Clean abstract `BaseTool` class, only existing search backend is Semantic Scholar. ~150-line PR. Headline JP project of 2025–2026. |
| 1b | **SakanaAI/AI-Scientist (v1)** | ~14k | Same architecture as v2 (Semantic Scholar + OpenAlex) | Paired drop with v2. v1 carries more stars / outreach value; v2 is the cleaner engineering target. Ship to both. |
| 2 | **pfnet-research/plamo-examples** | ~25 | New notebook `examples/web_search_youcom.ipynb` (PLaMo + LangChain + You.com retriever) | Low stars but PFN brand. PLaMo Prime is now a paid API, so a working web-search example is genuinely useful to PFN customers. |
| 3 | **llm-jp/awesome-japanese-llm** | ~1.4k | Markdown PR adding You.com under Tools/Search | Not a fork target — positioning play. NII-consortium-maintained canonical JP-LLM index. |

All four verified clean of existing You.com / `ydc-index` references (recon §4).

---

## 3. Channels

- **npaka (布留川英一)** — single highest-leverage JP voice for LangChain / agent / RAG content. X: `@npaka123`. note.com: https://note.com/npaka. Direct precedent for our pitch: he wrote up Tavily Search API + LangChain at https://note.com/npaka/n/n9fe8a607c56e. He reads English fine; the artifact we send him needs to be in Japanese.
- **llmdev.jp Discord** — kawakamimoeki's JP LLM-developer community. The recon flagged the GitHub URL (https://github.com/kawakamimoeki/llmdev.jp) returned 404 on direct fetch and likely renamed; **re-verify the invite link before sending anything**. Channels include Models, Vector DBs, LangChain, LLM app UX — the right audience.
- **awesome-japanese-llm listing PR** — https://github.com/llm-jp/awesome-japanese-llm. Add You.com to Tools/Search section once at least one code PR has landed (so the listing has something to point at).
- **PyCon JP 2026 CFP** — https://pretalx.com/pyconjp2026/cfp. Talk angle: "オープンソース日本語エージェントに本物のWeb検索を" (Real web search for open-source Japanese agents). Verify deadline before treating as actionable.
- Secondary X handles: `@karaage0703` (broader maker/AI audience), `@SakanaAILabs`, `@PreferredNet`, `@llm_jp`.
- Secondary writers: Qiita — `aokikenichi`, `Yasushi-Mo`, `kenken38`. Zenn — `ryuhat`, `kewa8579`.

---

## 4. Outreach approach (realism check)

This is a **translate + influencer** play, not a from-scratch authoring play.

1. **PRs to Sakana / PFN go in English.** Both orgs operate in English internally. Open AI-Scientist-v2 first, then mirror to v1, then submit the `plamo-examples` notebook PR. Do not attempt a JP-language PR description — it adds noise and can't be reviewed faithfully by us if maintainers ask follow-up questions.
2. **DM npaka in English** with the camel-ai + gpt-researcher fork links once landed. Subject line in English; he'll click through. Do not send him a machine-translated JP DM — it reads worse than English. The artifact he reposts should be the JP article from step 3, not the DM.
3. **Pay a JP technical translator** (~$200–400, one-time) for one ~1500-word piece, cross-posted to Qiita and Zenn under one team-member's account. Topic: "Sakana AI AI-Scientist + You.com Search API で本物のWeb検索を." Cross-link from the EN blog. **Do not author JP technical content directly.** It reads as low-effort to JP devs and damages credibility — every JP-LLM contributor on the receiving end can tell within two paragraphs.
4. **PyCon JP CFP** — submit a talk on the multilingual-integration story. Co-present with the translator from step 3 if we don't have a JP-native speaker by submission time. Confirm the 2026 deadline before committing internally.
5. **awesome-japanese-llm PR** — only after the Sakana PR is merged. Listing without a working code link is the wrong order.
6. **llmdev.jp Discord drop** — the JP Qiita/Zenn article from step 3 is the right artifact to share; do not post a separate JP-original message written by us.

---

## 5. Who can drive this on the team

- **Tuna** studied some Japanese — enough to read and proofread, not enough to author the technical Qiita/Zenn piece at native quality. Useful as the second pair of eyes on the translator's draft and as the on-the-ground liaison for npaka follow-ups.
- **Tuna + Muhammad** pair on the engineering work (the Sakana PR and PLaMo notebook are both English-language code changes — no JP needed).
- **Hire a JP technical translator** for the content leg. This is a fixed-budget item, not an ongoing role.
- **No current team member can carry sustained JP-language outreach alone.** Be honest about this in planning — the JP play needs either (a) a long-arc relationship with a JP collaborator who'll co-sign content, or (b) repeated one-off translation contracts. Do not commit to a JP content cadence the team can't sustain.

---

## 6. Sources

Full source list in `research/japanese-community.md` §5. Key links:
- https://github.com/SakanaAI/AI-Scientist-v2
- https://github.com/SakanaAI/AI-Scientist
- https://github.com/pfnet-research/plamo-examples
- https://github.com/llm-jp/awesome-japanese-llm
- https://note.com/npaka, https://twitter.com/npaka123
- https://pretalx.com/pyconjp2026/cfp
