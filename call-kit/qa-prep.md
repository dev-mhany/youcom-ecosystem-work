# Q&A Prep — Anticipated You.com Questions

**Speaker:** Gitonga Miriam
**Use:** keep this open on the second monitor during the call.
Each entry is **Q → short answer → backup detail → source**.

Short answer is what Gitonga says first. Backup detail is what she
adds if the asker presses or asks "say more." Source is the file
she'd cite if asked "where did you get that?"

Tier 1 questions (high probability) are first; Tier 2 (medium
probability) follow. Defer-to-Golda items are at the end.

---

## Tier 1 — high probability

### Q1. Why these two repos and not [framework X]?

**Short:** "Two reasons. First, both have a clear integration
entry-point file and a per-engine pattern we could mirror cleanly.
Second, they aren't covered yet — LangChain, LlamaIndex, DSPy, and
Composio already ship first-class You.com integrations, so we
deliberately picked uncovered ground."

**Backup:** "If `[framework X]` is on our Tier 2 list — Sakana AI,
smolagents, Agno, pydantic-ai, Qwen-Agent, local-deep-research — it's
the next batch. If it's a Tier 3 like Haystack or Semantic Kernel,
we'd want a region lead confirmed first. The full tier table is in
section 2 of the proposal."

**Source:** `proposal/MAIN.md` sections 1, 2.

---

### Q2. How will you handle rate limits and API key scoping for multiple regions?

**Short:** "The gpt-researcher fork already supports per-request key
override via `headers['you_api_key']` — the Tavily-shape multi-tenant
pattern. We'd propose per-region keys with usage caps, plus one shared
CI key for integration smoke tests."

**Backup:** "Rate limits surface via the `X-RateLimit-Limit`,
`Remaining`, and `Reset` headers — both forks log a warning and
soft-fail on 429 rather than crashing. For multi-region, the cleanest
shape is one key per regional lead so spend attribution is per-region
out of the box."

**Source:** `research/api-reference.md` section 3,
`research/gpt-researcher-build-report.md` section "Files touched"
(`headers.get('you_api_key')` per-request override).

---

### Q3. What's your QA / eval approach?

**Short:** "We want to align with the You.com team's existing eval
harness. Every fork ships with at least one locale-positive case and
one locale-negative case — for camel-ai that's `country='JP',
search_lang='ja'` and `search_lang='ar'`."

**Backup if pressed on the harness specifically:** "We're aware Eddy
Nassif leads the eval harness work. If outside contributors can extend
it, we'd want to add the new framework backends — particularly the
Arabic and Japanese locale dimensions, which the existing public
benchmarks don't cover deeply."

**Source:** `research/eddy-investigation.md`, `proposal/MAIN.md`
section 5.1.

---

### Q4. What do you need from us to ship the next five forks?

**Short:** "Three things. API keys for the team — minimum one per
regional lead plus a shared CI key. A direct channel with DevRel for
schema questions, especially on `you-contents`. And optional pre-PR
review of the next target list before we open upstream."

**Backup:** "On the channel, Slack, Discord, or shared email thread
— whichever fits your existing partnerships flow. On pre-PR review,
we'd rather route through You.com once and ship cleanly than have a
maintainer ask 'did You.com sign off on this?' after the fact."

**Source:** `proposal/MAIN.md` section 5.2.

---

### Q5. Are you going to push these forks upstream?

**Short:** "Not until you acknowledge the approach. What we're showing
today is reference forks under our company org — local commits, no
upstream PRs, no maintainer surprises."

**Backup:** "Once we have a green light on direction, the upstream PR
flow is scaffold-implementation-tests-docs in separate commits, in
English, with locale-test fixtures included. Same cadence visible in
the existing fork commit logs."

**Source:** `proposal/MAIN.md` section 3 (forks unpushed), section 5.1.

---

### Q6. What's the team composition?

**Short:** "Multi-region. Amr Nabil leads the Arabic side, with
Muhammad Hany on the Arabic team — Muhammad authored the camel-ai
fork branch. Tuna leads the Japanese leg, supported by paid
translation for JP-language content. The team is Python-strong, with
prior open-source work in adjacent ecosystems."

**Backup if asked for specifics:** "Headcount and engagement scope I'd
want Golda to walk through — that's a contracting conversation, not
my call."

**Source:** `proposal/MAIN.md` section 4 (Arabic — Amr Nabil lead with
Muhammad on team; Japanese — npaka outreach plus paid translator);
`research/japanese-community.md` section 3.

---

### Q7. Pricing and scope?

**Short:** "I'd want Golda to walk through that — she has the
framing."

**Backup:** None. Hand over to Golda or note "we can follow up by
email this week."

**Source:** Defer — see Defer-to-Golda section below.

---

### Q8. Why didn't you do CrewAI?

**Short:** "CrewAI is already covered. They shipped You.com
integration documentation in 1.14.4, end of April — agents connect to
api.you.com/mcp via CrewAI's existing MCP support. So adding a CrewAI
fork would duplicate something already shipped."

**Backup if pressed:** "There's no CrewAI-specific Python class for
You.com — the integration code lives on your side as the MCP server.
CrewAI's contribution was the docs recipe and the release-note
listing. Either way, that ground is covered, which is why we picked
camel-ai and gpt-researcher instead."

**Source:** `research/crewai-verification.md` section 1, 2c.

---

### Q9. Eddy mentioned doing X — were you aware?

**Short — if "X" sounds like the eval harness:** "Yes — Eddy Nassif's
eval harness is the comparison target we want our forks measured
against. We cited it in the proposal."

**Short — if "X" is something else / unclear:** "I'd want to check
that — can we follow up by email so I get the detail right?"

**Backup if pressed on harness methodology:** "We didn't dig into the
methodology in this pass; that's exactly the kind of conversation we'd
want a direct channel with DevRel for."

**Source:** `research/eddy-investigation.md` (Eddy Nassif, Senior
Applied Scientist, eval-harness lead).

---

### Q10. Multi-region is a buzzword — what's actually different in your code?

**Short:** "Both forks expose `country` and `language` as first-class
parameters on the Search endpoint. The upstream LangChain wrapper
exposes those for News only, not for Search."

**Backup:** "On screen-share I can show the docstring for `search_you`
in camel-ai — the examples explicitly cover `country='SA',
search_lang='ar'` for Arabic and `country='JP', search_lang='ja'` for
Japanese. The unit tests assert that those values propagate to the
HTTP params correctly. To be clear: that's the code path. We have not
yet run a live Arabic or Japanese query against the API — getting an
API key for that is one of the asks."

**Source:** `research/camel-build-report.md` section 4
(`test_search_you_multilingual_params_propagated`),
`proposal/MAIN.md` section 1 (LangChain Notes), section 4 (Arabic
locale verification status).

---

### Q11. How do you handle errors? What's the failure mode?

**Short:** "Both integrations soft-fail rather than crashing. On a
missing API key, the gpt-researcher retriever logs a warning and
returns an empty list — the Tavily pattern. On HTTP errors, both
return empty rather than propagating an exception."

**Backup:** "The camel-ai version raises explicitly on missing key
because that's the camel-ai convention via `@api_keys_required` —
which itself raises a clear `ValueError`, not a silent failure. Both
patterns match what their respective upstream codebases already do
for other engines. I can show the soft-fail test in the
gpt-researcher run if useful."

**Source:** `research/gpt-researcher-build-report.md` section "Test
run" (`test_search_soft_fails_on_missing_key`),
`research/camel-build-report.md` section 4 (`@api_keys_required`).

---

## Tier 2 — medium probability

### Q12. Why camel-ai specifically? It's not the biggest framework.

**Short:** "KAUST origin. camel-ai was founded out of the Saudi
research scene, and Muhammad's outreach into KAUST and AUC is the
warmest Arabic-region path we have. It's also a clean
multi-engine `SearchToolkit` — fifteen existing search backends, all
following the same pattern. You.com slots in cleanly."

**Source:** `proposal/MAIN.md` section 2 (Tier 1 table — KAUST origin
note), section 4 (Arabic).

---

### Q13. Why gpt-researcher specifically?

**Short:** "Closest framework analog to your Research API. Their 'deep'
mode is the natural counterpart. The codebase already has a
Tavily-shape contract, which we modeled on directly, plus existing
Serper-style locale precedent for `country` and `language`."

**Source:** `proposal/MAIN.md` section 2 (Tier 1 table —
gpt-researcher row).

---

### Q14. Have you run live Arabic or Japanese queries?

**Short:** "No — not yet. The unit tests cover the locale code path,
but a live integration smoke test against a real Arabic or Japanese
query is the first thing we'd want a You.com API key for."

**Backup:** "We had two budgeted live API calls for the build pass.
One went to the wrong host and returned 403; the second confirmed the
documented endpoint with a 200 on an English query. Spending a third
on Arabic verification wasn't budgeted."

**Source:** `research/api-reference.md` section 5 (multilingual
support — documented but unverified-by-this-pass).

---

### Q15. What about LlamaIndex / DSPy — those have first-class You.com integrations, right?

**Short:** "Yes, both first-class. LlamaIndex has a `YouRetriever`,
DSPy has `YouRM` written by christeefy. We treated both as covered
and didn't deep-dive their parity contracts. LangChain was the one we
did benchmark against — it's the most complete first-party surface."

**Source:** `proposal/MAIN.md` section 1 (LlamaIndex / DSPy / LangChain
notes).

---

### Q16. What's the cadence you're proposing?

**Short:** "Two forks per month upstream-PR-ready, minimum. The
camel-ai and gpt-researcher work was the proof of cadence — both built
in the engagement prep window, two weeks. Scaling beyond that depends
on team availability and confirmed regional leads."

**Source:** `proposal/MAIN.md` section 5.3.

---

### Q17. How do you reconcile parity with LangChain when LangChain doesn't expose locale on Search?

**Short:** "We exceed parity on the locale dimension specifically — and
that's intentional. LangChain restricts `country` and `language` to the
News endpoint. Our forks expose them on Search because the documented
API supports them and the multi-region story needs them."

**Backup:** "Everywhere else, parity is the contract. Same env var
naming convention, same auth header, same response field handling. The
delta is locale-on-Search and that's documented in both build reports."

**Source:** `proposal/MAIN.md` section 1 (LangChain Notes — locale gap),
section 3 intro.

---

### Q18. What if the upstream maintainers reject the PRs?

**Short:** "Then the forks remain useful as standalone reference code
and we route via documentation rather than upstream merge — the same
shape CrewAI ended up with. We'd also want a conversation with You.com
DevRel before opening the PRs to reduce that risk."

**Source:** `research/crewai-verification.md` (docs-route precedent),
`proposal/MAIN.md` section 5.2 (pre-PR review ask).

---

### Q19. The upstream LangChain integration is in `langchain-community`, not `langchain` core — does that matter?

**Short:** "Not for our purposes. langchain-community is where every
integration lives now — the package was split out. The path
`libs/community/...` in the core repo is the old location. The
benchmark we built to is the current `langchain-community` wrapper."

**Source:** `proposal/MAIN.md` section 1 (LangChain Notes).

---

### Q20. What about the Research API — are the forks using it?

**Short:** "Not yet. Both forks target the Search endpoint
(`ydc-index.io/v1/search`). Adding a `topic='research'` branch that
hits `api.you.com/v1/research` is a natural next iteration for
gpt-researcher specifically — it's noted as an open question in our
build report."

**Source:** `research/gpt-researcher-build-report.md` section "Open
questions" item 3, `research/api-reference.md` section 1.3.

---

### Q21. How does the team handle the language gap on the Japanese side if no one is JP-native?

**Short:** "We don't write JP-language technical content directly.
Translation is paid — roughly two to four hundred dollars per Qiita
plus Zenn cross-post. The outreach lead for JP is npaka, who reads
English fine; the artifact we'd send him is in Japanese, the DM
itself is English."

**Backup:** "We're also not presenting in machine-translated Japanese
at PyCon JP. If we get a CFP slot, we partner with a JP-native
co-presenter."

**Source:** `research/japanese-community.md` section 3.

---

### Q22. Why isn't there a Sakana fork already if you're talking about Japan?

**Short:** "Sakana is Tier 2 — next engagement. We deliberately scoped
this pass to two forks so we could ship them complete rather than four
half-done. Sakana is the first thing on the next list."

**Source:** `proposal/MAIN.md` section 2 (Tier 2 table — Sakana row),
section 4 (Japanese — NEXT engagement).

---

### Q23. Are these forks tested against the actual You.com Search API or against mocks?

**Short:** "Mocks for unit tests, plus one captured live 200 response
that the test fixtures mirror. The live-API integration smoke tests
are pending an API key for the team."

**Backup:** "The captured response is at
`research/fixtures/search-response.json`, taken 2026-05-15 against the
documented endpoint. Schema in our forks matches that capture exactly
— we cross-checked every field name."

**Source:** `research/api-reference.md` section 1.1 (response schema —
verified live), `research/camel-build-report.md` section 5
(deviations — inlined fixture).

---

## Defer-to-Golda

These questions Gitonga should not try to answer. Use the exact
phrasing below — it's polite and clean.

**Default deflection phrasing:**

> "I'd want Golda to weigh in on that — can we follow up by email?"

Or, if Golda is on the call:

> "Golda — do you want to take that one?"

### Defer list

- **Pricing.** Engagement cost, hourly rates, retainer structure,
  any dollar figures.
- **Contract terms.** Length of engagement, exclusivity, scope-change
  clauses, IP ownership of the forked code.
- **Cooperation.org headcount.** Total team size, who reports to
  whom, hiring plans, regional staffing levels beyond the named leads.
- **Budget for travel** (e.g., PyCon JP attendance, KAUST visits).
- **API credit asks denominated in dollars** — defer the dollar
  framing; it's fine to say "API keys for the team and one shared CI
  key" but not "X dollars of credits per month."
- **Anything about other Cooperation.org clients.** Not for this call.
- **Anything about You.com competitors' commercial terms** (Tavily
  pricing, Serper enterprise tier, etc.). We talk about them as
  technical precedent only.
- **Future commitments beyond the proposal scope.** If You.com asks
  "could you also do [adjacent thing]," answer "I'd want Golda to
  scope that with you."

If unsure whether something is a defer item, defer. The cost of
deferring something Golda would have answered is zero. The cost of
answering something Gitonga shouldn't have is real.
