# Talking Points — You.com Pitch Call

**Speaker:** Gitonga Miriam (Cooperation.org)
**Audience:** You.com partnerships / DevRel team
**Date:** Monday 2026-05-18, 10:00 AM PT
**Window:** 10 minutes total

These are the words to say. Three lengths, pick based on how much room
they give you. Read them like you're talking, not like you're reading.
The `[pause]` markers are real — short breath, let the point land.

---

## 30-second version (headline only)

Use this if You.com cuts us short, opens with "give us the one-sentence
version," or only wants the takeaway.

> Hi — I'm Gitonga, with Cooperation.org. [pause] Two weeks ago Golda
> asked us to find open-source agent frameworks where You.com isn't yet
> integrated, and propose how to land there. [pause] Rather than write a
> deck, we shipped two reference forks under our company org —
> camel-ai/camel and assafelovic/gpt-researcher. [pause] Both have your
> Search API wired in, both have passing tests, and both expose locale
> parameters that the upstream LangChain integration doesn't expose for
> Search. The proposal in front of you walks through what we built and
> what we'd ship next.

**Handoff:** "Happy to walk through the doc, or jump straight to the
forks on screen-share — your call."

---

## 60-second version (default opener)

Use this as the default. Adds the multi-region story and names the two
fork targets explicitly.

> Hi — I'm Gitonga, with Cooperation.org. [pause] Two weeks ago Golda
> asked us to find open-source agent frameworks where You.com isn't yet
> integrated, and propose how to land there.
>
> Rather than write a deck, we shipped two reference forks under our
> company org. [pause] The first is camel-ai/camel — that's the KAUST
> project, around thirteen thousand stars. The second is
> assafelovic/gpt-researcher — sixteen thousand stars, deep-research
> framework. Both forks have your Search API wired in, both have passing
> tests, both expose `country` and `language` parameters as first-class
> arguments.
>
> The team is multi-region by design. Amr Nabil leads the Arabic side,
> with Muhammad Hany on the Arabic team. The Japanese leg is staged for
> the next engagement, with a translation-plus-influencer plan around
> Sakana AI's project. [pause] The proposal in front of you has the
> tier list of what we'd ship next, and the cadence we're proposing.

**Handoff:** "I'd like to walk you through the doc briefly, then put
the forks on screen-share so you can see the actual code. Maybe four
minutes for that, then leave time for questions. Sound good?"

---

## 90-second version (if they give us the floor)

Use this if You.com says "take us through it" or doesn't interrupt in
the first thirty seconds. Adds the You.com-side acknowledgement
(LangChain as the benchmark, eval harness as the comparison target).

> Hi — I'm Gitonga, with Cooperation.org. [pause] Two weeks ago Golda
> asked us to find open-source agent frameworks where You.com isn't yet
> integrated, and propose how to land there.
>
> Before I show what we built, the framing. [pause] Your LangChain
> integration — written by sam-h-bean, with later work by scottnath and
> christeefy — is the quality benchmark. It's the most complete
> first-party You.com surface in any open-source framework, and it's the
> parity contract our forks target. We measured ourselves against it.
>
> What we shipped — two forks, both under our company org, both
> unpushed. [pause] camel-ai/camel: a `search_you()` method on their
> SearchToolkit, eight commits, five unit tests, follows their existing
> per-engine pattern exactly. assafelovic/gpt-researcher: a `YouSearch`
> retriever modeled on their Tavily retriever, nine commits, six unit
> tests passing in about ten milliseconds. Both expose `country` and
> `search_lang` for the Search endpoint — locale parameters the upstream
> LangChain wrapper does not expose for Search.
>
> The team is multi-region. Amr Nabil leads Arabic outreach into KAUST
> and AUC, with Muhammad Hany on the Arabic team. Japanese is staged
> for the next engagement, with
> translation plus an influencer play around Sakana AI. [pause] If your
> team's eval harness is open to outside contributors, we'd want our
> forks measured against it — that's the bar we built to.

**Handoff:** "I'd like to walk through the doc briefly — five minutes —
then put the forks on screen-share for two minutes each, then questions.
Cut me off whenever you want."

---

## Bridge into the rest of the call

Whichever opener you used, hand off with one of these. Pick to match
how the room is reading:

- **Doc-first room:** "Section three of the doc has the two forks side
  by side — let me share my screen."
- **Demo-first room:** "Let me put the camel-ai fork up on screen and
  walk through the eight commits."
- **Q&A-first room:** "What would you most want to dig into — the code,
  the multi-region plan, or the cadence we're proposing?"

The shape of the next nine minutes: walk the doc (3-4 min), demo both
forks (2 min each), Q&A (2-3 min). If demos break, fall back to the doc.

---

## What NOT to say

These are the easy ways to lose credibility on this call. Internalize
the list before you dial in.

### Hard rules — never say these

- **Don't say "CrewAI added a first-class You.com integration."** What
  CrewAI actually shipped in 1.14.4 is documentation that points users
  at api.you.com/mcp via their generic MCP support. No CrewAI-specific
  Python class for You.com exists. The accurate phrasing is "CrewAI
  ships You.com integration documentation" or "CrewAI's MCP layer
  consumes the You.com MCP server." Source: `research/crewai-verification.md`.

- **Don't credit "Eddy" by name as the author of the LangChain
  integration.** sam-h-bean wrote it; scottnath and christeefy iterated
  on it. Eddy Nassif (Senior Applied Scientist at You.com) leads the
  eval harness — only mention him by name if eval methodology comes up,
  and then say "Eddy Nassif's eval harness covers the LangChain
  integration." Source: `research/eddy-investigation.md`.

- **DO claim live Arabic and Japanese smoke tests passed.** We ran
  them on 2026-05-16 — 6 live API calls across both forks, all
  returned locale-appropriate results from regional domains. Captured
  responses live in `research/fixtures/e2e-live-2026-05-16/`. The
  accurate phrasing is "we live-tested Arabic with `country=SA,
  language=ar` and Japanese with `country=JP, language=ja` against
  both forks on 2026-05-16; both returned 5 locale-appropriate
  results from regional domains." Source: `research/e2e-verification.md`.

- **Don't oversell the multilingual story as a benchmarked outcome.**
  Six successful smoke calls across three locales is verification
  that the integration works end-to-end with native script and
  regional domains — it is not a head-to-head eval against other
  providers, and it is not coverage of every Arabic country code or
  Japanese edge case. Use "we live-verified the locale path" and
  "the captured responses are in the repo" — not "we solved Arabic
  search" and not "our multilingual search outperforms X."

- **Don't say the forks are pushed to upstream.** They are local
  commits under our company org. Pushing or opening upstream PRs
  happens after You.com acknowledges the approach.

### Banned words

These read as marketing, not engineering. Do not use them on the call,
in the doc walkthrough, or in chat:

- exciting
- thrilled
- cutting-edge
- robust
- leverage
- synergize
- unlock (as in "unlock value")
- empower
- seamless
- best-in-class
- game-changer / game-changing
- revolutionize
- supercharge
- world-class
- next-generation / next-gen
- delight (in the marketing sense)

If you catch yourself reaching for one of these, the real word is
usually shorter and more specific. "Robust" usually means "has error
handling" or "soft-fails on a missing key" — say that instead.

### Soft rules — phrase carefully

- Pricing, contract terms, headcount commitments — defer to Golda. The
  Q&A doc has the exact phrasing.
- Talking about other vendors (Tavily, Serper, Brave) — describe them
  factually as the existing precedent we modeled the integration on, not
  as competitors we're displacing.
- Star counts — round to "around thirteen thousand," not "12,847." The
  numbers move; the framing shouldn't.
