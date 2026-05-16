# Arabic-Region Outreach Kit

Owners on this engagement: **Amr Nabil** (lead) and **Muhammad Hany**. The integration target is camel-ai/camel (KAUST-founded, Saudi roots). This kit gives the Arabic team concrete contacts and English-source templates to translate in their own voice.

---

## 1. Why this region matters

KAUST (King Abdullah University of Science and Technology, Saudi Arabia) is the institutional home where camel-ai was founded — that is the single warmest link between You.com and the Arabic-language research community. AUC (American University in Cairo) is the strongest CS research program in the Arab world by publication output and industry placement. MBZUAI (Mohamed bin Zayed University of AI, Abu Dhabi) runs an active Arabic NLP group and ships Arabic LLM work (Jais, AceGPT lineage). Arabic queries are a known weak spot for English-default search APIs — most fall back to transliteration or English results, which makes a properly localized `country=SA` / `search_lang=ar` parameter pair an immediate, demonstrable win.

---

## 2. Channels and contacts

Concrete handles where confirmed; verify before sending.

### Universities and labs
- **KAUST Generative AI** initiative — https://cemse.kaust.edu.sa/ai (CEMSE division). The camel-ai founding team came out of this division; warmest possible intro path.
- **KAUST AI Initiative on X** — `@KAUST_News`, `@kaust_ai` (verify; KAUST runs several institutional accounts).
- **MBZUAI Arabic NLP group** — https://mbzuai.ac.ae/research-departments/nlp/. Lead faculty: Timothy Baldwin (Provost, NLP), Preslav Nakov (NLP dept chair, Arabic-fact-checking work). Public lab page lists ongoing Arabic NLP projects.
- **MBZUAI on X** — `@mbzuai`.
- **AUC CS faculty** — https://sse.aucegypt.edu/departments/computer-science. Dept seminar series is a public-facing entry point. Look for faculty publishing in EMNLP/ACL Arabic-NLP tracks the past two years.
- **Cairo University NLP group** (Aly Fahmy lineage) — secondary; older Arabic-NLP center.

### Forums and chat
- **r/MENA** (https://reddit.com/r/MENA) — general MENA tech/career sub, ~250k subs. Moderate fit; flag-as-low-effort risk if posted as a launch ad.
- **r/Saudi_AI** — does not exist as of writing (verify; r/saudiarabia exists but is general).
- **r/arabs** (~150k) — general; same caveat as r/MENA.
- **Arabic AI Discord servers** — there is no single canonical server equivalent to llmdev.jp. Two communities worth checking and verifying before posting:
  - **Hugging Face Arabic NLP** — community channel on the HF Discord, language-tagged.
  - **Cohere For AI / Aya community** — maintains the Aya multilingual project, has active Arabic contributors and a public Discord. https://cohere.com/research/aya
- **Arabic-language LLM Slack/Discord around AraBench / AraGen** — verify per-eval-suite; these tend to be short-lived around paper-release windows.

### X / Twitter Arabic AI handles (verify each)
- `@CamelAIOrg` — camel-ai's own handle (English-language, but the founding team's Arabic-region ties live here)
- `@mbzuai` — MBZUAI institutional
- `@aiasb_ai` — Arabic AI Society / المجتمع العربي للذكاء الاصطناعي (verify spelling; group maintains public Arabic AI bulletins)
- `@SDAIA_SA` — Saudi Data and AI Authority (government, but reposts ecosystem news)
- `@G42ai` — UAE-based G42 (Jais model lineage with MBZUAI)
- `@inceptionai` — Inception AI (G42 subsidiary, ships Jais)
- `@aielazhar` (verify) — Al-Azhar AI initiatives, Egyptian academic side
- Individual researchers: verify current handles for Preslav Nakov, Hassan Sajjad, Bashar Talafha (Jais-team contributors). Better to land an academic-tone email than a cold X DM for these.

### Conferences and venues
- **ArabicNLP workshop** (collocated with EMNLP) — annual, runs the AraGen / AraEval shared tasks. The workshop website lists organizers and accepts demo papers.
- **WANLP** — Workshop on Arabic Natural Language Processing (older name; merged into ArabicNLP).
- **KAUST Rising Stars in AI** symposium — annual, KAUST-hosted, attracts the regional grad-student pipeline.

---

## 3. Templates (English source — Muhammad translates)

Each ≤100 words. Plain English. No marketing copy.

### Template A — to KAUST / AUC / MBZUAI researchers

> Subject: camel-ai + You.com Search — Arabic locale support, would value your input
>
> Hi <their-name>,
>
> I work with You.com on the camel-ai search toolkit. We just landed a `search_you()` method in `camel/toolkits/search_toolkit.py` that exposes `country` and `search_lang` parameters — so `country="SA"` + `search_lang="ar"` returns properly localized Arabic results, not transliterated English fallbacks.
>
> I'd value your read on which Arabic-language eval benchmark we should target next (AraGen? AraBench? something internal?). Happy to share API credits for any eval work your group wants to run.
>
> Code: <PR-link>. Paper hook: <their-recent-paper>.
>
> — Muhammad

### Template B — to community Discord / forum

> We just shipped a multilingual search backend for camel-ai — the new `search_you()` toolkit method takes `country` and `search_lang` parameters, so Arabic and Japanese queries return locale-correct results instead of English fallbacks.
>
> Two-line example:
> ```
> tk = SearchToolkit()
> tk.search_you(query="<arabic-query>", country="SA", search_lang="ar")
> ```
>
> PR: <link>. API key signup: <link>. If anyone wants to run this against AraGen or a benchmark you trust, ping me — happy to share credits.
>
> — Muhammad (camel-ai contributor)

### Template C — to a JP/AR-bilingual contributor

> Hi <their-name>,
>
> Saw your work on <their-recent-paper-or-repo>. We just landed a multilingual search integration in camel-ai (PR: <link>) with first-class Arabic and Japanese locale params. The next step is a side-by-side eval suite — Arabic queries against AraGen-style prompts, Japanese against <jp-bench>, scored on locale-correctness not just BLEU.
>
> Would you be open to co-authoring the eval methodology and a short writeup? You.com would cover API costs. Looking for one collaborator on each language; if either side fits, I'd love a 30-minute call.
>
> — Muhammad

---

## 4. Translation note for Muhammad

Translate these in your own voice. **Do not run them through a translator.** Arabic-speaking technical readers spot machine-translated outreach in the first sentence — wrong register, wrong politeness particles, formal MSA where dialect would land warmer or vice versa. Add the cultural framing you'd use naturally: greeting style, university-honorific norms, region-appropriate signoff. The English above is a content brief, not a script. If a phrase doesn't carry into Arabic cleanly, drop it rather than calque it.

For the academic-facing Template A, default to MSA. For Template B (community Discord), default to whatever register that specific server uses — most lean toward English-mixed Gulf or Egyptian dialect for technical content; match what's already in the channel. For Template C (1:1 to a researcher), match the recipient's own published voice if you can find a sample.

---

## 5. Sources

- https://www.camel-ai.org/ (camel-ai org)
- https://github.com/camel-ai/camel (codebase)
- https://cemse.kaust.edu.sa/ai (KAUST CEMSE / AI)
- https://mbzuai.ac.ae/research-departments/nlp/ (MBZUAI NLP)
- https://sse.aucegypt.edu/departments/computer-science (AUC CS)
- https://cohere.com/research/aya (Cohere For AI / Aya)
- ArabicNLP / WANLP workshop series (collocated with EMNLP)
