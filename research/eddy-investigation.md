# "Eddy" Investigation — Who Did Golda Mean?

**Context:** Golda Velez (You.com) told her team "check it against what eddy did i think in langchain." None of the named LangChain integration committers (sam-h-bean, scottnath, christeefy) go by Eddy. This report identifies the most likely Eddy.

---

## 1. Top candidate

**Eddy Nassif — Senior Applied Scientist, You.com** (Confidence: **HIGH**)

Eddy Nassif leads You.com's research team and built the **You.com Web Search Eval Harness** — a benchmarking framework that evaluates any web search provider, used to validate You.com's APIs against competitors and against framework integrations (LangChain, LlamaIndex, DSPy — all three of which are forked into the `You-OpenSource` GitHub org).

Evidence:
- You.com blog post (April 21, 2026): "The You.com Web Search Eval Harness: Benchmark Any Web Search Provider Yourself," authored by **Eddy Nassif, Senior Applied Scientist**. Source: https://you.com/blog (post listing).
- You.com Research API launch page describes the same person ("Eddy N., Senior Applied Scientist") as having "refined this harness over years of research, competition wins, and running these benchmarks against their own production systems." Source: https://you.com/resources/research-api-by-you-com
- RocketReach lists "Eddy N." at You.com as Senior Applied Scientist, Seattle, Georgia Tech MS '21–'24. Source: https://rocketreach.co/eddy-n-email_761534995
- The `You-OpenSource` GitHub org explicitly hosts forks of `langchain`, `llama_index`, and `dspy` — exactly the integrations Eddy's harness would benchmark. Source: https://github.com/You-OpenSource

**Why this matches Golda's phrasing:** "check it against what eddy did i think in langchain" reads naturally as "validate against Eddy's eval-harness work, which covered the LangChain integration." Golda is asking the team to compare new behavior against Eddy's existing benchmark — not crediting him with writing the integration code. The "i think" hedge is consistent with her remembering that Eddy's harness *covered* LangChain even if she's unsure which framework specifically.

---

## 2. Other candidates

1. **Eddy Liang** (LinkedIn: linkedin.com/in/yuedi-liang/) — confirmed at You.com, but no public signal he touched LangChain, ecosystem, or open-source integrations. Profile activity is around You.com's Series B funding, suggesting business/comms/ops rather than engineering. **Low.**
2. **Eddy at LangChain (indirect theory)** — no Eddy surfaced on LangChain's partnerships/ecosystem team in searches. **Very low.**
3. **Christopher Tee going by "Eddy"** — no evidence anywhere; profile is consistently "Chris/Christopher." **Ruled out.**
4. **Phonetic variants (Eddie/Edi/Adi/Yidi)** at You.com — none surfaced beyond Eddy Liang and Eddy Nassif. **None.**

---

## 3. Negative findings (confirmed not "Eddy")

- **sam-h-bean / "mrbean" / Samuel Bean** — Senior Staff Engineer, You.com (now Microsoft AI). No Eddy nickname. https://github.com/sam-h-bean | https://www.linkedin.com/in/samuel-h-bean/
- **scottnath / Scott Nath** — Front-end UI DevOps architect, NYC, ex-IBM/NBCUniversal. No Eddy nickname. https://github.com/scottnath
- **christeefy / Christopher Tee** — Toronto. Personal sites (christophertee.dev, garden.christophertee.dev) all say "Chris/Christopher." No Eddy. https://github.com/christeefy

---

## 4. Recommendation for the call

**(b) Credit "the You.com team" generically — and only mention Eddy by name if the conversation goes deeper into eval/benchmarking.**

Specifically: Gitonga should *not* lead with "Eddy did the LangChain integration" because that's factually wrong (sam-h-bean wrote it; scottnath and christeefy iterated on it). But if Eddy's eval work comes up, it's safe and accurate to say **"Eddy Nassif's eval harness covers our LangChain integration."**

Confidence is high enough on Eddy = Eddy Nassif that Gitonga doesn't *need* to ping Golda first — but if there's any doubt, a one-line Slack DM ("quick check — by 'eddy' you mean Eddy Nassif's eval harness, right?") costs nothing and removes all risk of misattribution on a public call.

---

## 5. Sources

- https://you.com/blog (Eddy Nassif post listing, April 21, 2026)
- https://you.com/resources/research-api-by-you-com (Eddy N., Senior Applied Scientist, harness lead)
- https://rocketreach.co/eddy-n-email_761534995 (Eddy N. at You.com, Seattle, GaTech)
- https://github.com/You-OpenSource (You.com OSS org with langchain/llama_index/dspy forks)
- https://www.linkedin.com/in/yuedi-liang/ (Eddy Liang at You.com — alternate candidate)
- https://github.com/sam-h-bean | https://www.linkedin.com/in/samuel-h-bean/ (original LangChain integration author, not Eddy)
- https://github.com/scottnath (LangChain PR author, not Eddy)
- https://github.com/christeefy (LangChain + DSPy PR author, not Eddy)
- https://www.langchain.com/blog/you-com-x-langchain (Oct 18, 2023 guest post — no individual You.com bylines)
- https://docs.langchain.com/oss/python/integrations/tools/you (current You.com LangChain integration docs)
