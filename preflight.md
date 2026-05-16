# Sunday-Handoff Preflight

**Date:** 2026-05-15
**Prepared for:** Muhammad Hany (handing to Golda Velez Saturday evening)
**Review window:** 2026-05-16 evening through 2026-05-17
**Call:** 2026-05-18, 10:00 AM PT

---

## Top section — one-screen summary

The Cooperation.org pre-Monday packet is materially complete. The
proposal (`proposal/MAIN.md`), the three-file call kit
(`call-kit/demo-script.md`, `call-kit/qa-prep.md`,
`call-kit/talking-points.md`), the eight research backgrounders (in
`research/`), and the two reference forks
([Cooperation-org/camel](https://github.com/Cooperation-org/camel/pull/1),
[Cooperation-org/gpt-researcher](https://github.com/Cooperation-org/gpt-researcher/pull/1)) are all on disk and
internally consistent per the 2026-05-15 link-and-consistency audit.
Apply-ready single-file patches for both forks are in `patches/`. Live
end-to-end verification across English, Arabic, and Japanese was run
on 2026-05-16 — all 11 unit tests pass, all 6 live-API calls return
real locale-appropriate results, captured responses are in
`research/fixtures/e2e-live-2026-05-16/`, full report in
`research/e2e-verification.md`. Both forks have been pushed to
Cooperation-org with fork-internal review PRs open
(https://github.com/Cooperation-org/camel/pull/1 and
https://github.com/Cooperation-org/gpt-researcher/pull/1). What remains
open is bounded: a handful of items that need a human eyeball (X
handle, two LinkedIn profiles, the Eddy Nassif chain, one CrewAI
release wording, one n8n integration URL), and a few logistical items
Muhammad owns himself.

### Commit counts

| Repo | Path | Commits ahead of base | Total commits | Base |
|---|---|---|---|---|
| Working repo | https://github.com/dev-mhany/youcom-ecosystem-work | n/a (own history) | 25 | initial commit |
| camel-fork | https://github.com/Cooperation-org/camel (PR https://github.com/Cooperation-org/camel/pull/1) | 8 | 58 | `0d917c0f` upstream master |
| gpt-researcher-fork | https://github.com/Cooperation-org/gpt-researcher (PR https://github.com/Cooperation-org/gpt-researcher/pull/1) | 9 | 601 | `92bfc038` upstream main |

Working-repo total of 25 is as of this preflight commit (commit 24 adds
the patches; commit 25 adds this file).

### Doc tree

```
youcom-ecosystem-work/
  README.md
  preflight.md                                  (this file)
  call-kit/
    demo-script.md
    qa-prep.md
    talking-points.md
  patches/
    camel-fork.patch                            (8 commits, ~26 KB)
    gpt-researcher-fork.patch                   (9 commits, ~28 KB)
  proposal/
    MAIN.md
  research/
    api-reference.md
    benchmark-langchain.md
    camel-build-report.md
    camel-recon.md
    crewai-verification.md
    eddy-investigation.md
    gpt-researcher-build-report.md
    gpt-researcher-recon.md
    japanese-community.md
    link-audit.md
    fixtures/
      search-response.json
```

### Verdict

**Ready for Golda's review.** No P0 blockers. Open items are
review-class (decisions Golda needs to make) and prep-class (manual
verifications Muhammad needs to run). Section "Open questions for
Golda's review" enumerates them with severity.

---

## What Muhammad does with this — Slack-paste checklist for Golda

Copy the block below into Slack when handing off Saturday evening.
Adjust the greeting to taste.

```
Hey Golda — packet for Monday is ready. Three things to read, one
question per fork, and a handful of yes/no decisions for you.

Read in this order:
1. proposal/MAIN.md — the doc Gitonga is presenting (5 sections,
   ~350 lines)
2. call-kit/talking-points.md — 30/60/90-second openers + banned-
   words list
3. call-kit/demo-script.md — literal cmd-by-cmd runbook for the
   two-fork screen-share
4. call-kit/qa-prep.md — anticipated Q&A; defer-to-Golda items
   listed at the bottom

Forks (local-only, NOT pushed yet — push decision is yours):
- https://github.com/Cooperation-org/camel              (8 commits ahead; PR https://github.com/Cooperation-org/camel/pull/1)
- https://github.com/Cooperation-org/gpt-researcher     (9 commits ahead; PR https://github.com/Cooperation-org/gpt-researcher/pull/1)
- patches/camel-fork.patch and patches/gpt-researcher-fork.patch
  re-apply both branches onto a fresh upstream clone if anything
  goes sideways during push.

Five things I need a yes/no on before Sunday push:
1. "Eddy" attribution — confirm Eddy = Eddy Nassif (Sr Applied
   Scientist, eval harness lead). research/eddy-investigation.md
   has the full chain. One Slack reply to me removes all risk.
2. CrewAI 1.14.4 line — research/crewai-verification.md says
   "Documentation" classification. Eyeball the release page once.
3. Push to Cooperation-org GitHub now, or hold until after the
   call? Both forks are clean and patch-ready either way.
4. The npaka123 X handle and the two LinkedIn URLs in
   eddy-investigation.md — anyone on the team able to eyeball
   them before Sunday?
5. (Resolved 2026-05-16) Live Arabic + Japanese smoke tests
   completed against both forks. Captured responses are in
   `research/fixtures/e2e-live-2026-05-16/`; report in
   `research/e2e-verification.md`. All passed — locale-correct
   results from regional domains.

Full preflight detail: preflight.md (top of the working repo).
```

---

## What Muhammad MUST do himself before the call

Treat this list as Muhammad-only. None of these were doable from inside
the agent harness; all need a human.

### Manual verifications (from `research/link-audit.md` section 6)

- **`@npaka123` X handle.** Confirm it is the correct handle for npaka
  / Hidekazu Furukawa / 布留川英一. Cited in
  `research/japanese-community.md`.
  X bot-blocks programmatic checks (HTTP 403). Eyeball
  `https://x.com/npaka123` in a browser; confirm display name and bio
  match.
- **LinkedIn profiles.** Open in a browser:
  - `https://www.linkedin.com/in/samuel-h-bean/` — confirm Samuel H.
    Bean, ex-You.com Senior Staff Engineer, now at Microsoft AI.
  - `https://www.linkedin.com/in/yuedi-liang/` — confirm Yuedi
    ("Eddy") Liang at You.com. (LinkedIn returns HTTP 999 to
    unauthenticated bots; eyeball is the only check.)
- **Eddy Nassif identification chain.** The reasoning in
  `research/eddy-investigation.md` is circumstantial (RocketReach +
  you.com/blog + you.com/resources + You-OpenSource GitHub org). Per
  the doc's own §4 recommendation, a one-line Slack DM to Golda
  ("quick check — by 'eddy' you mean Eddy Nassif's eval harness,
  right?") removes all risk. Send it.
- **CrewAI 1.14.4 release wording.** `research/crewai-verification.md`
  quotes the release notes verbatim ("Add You.com MCP tools for
  search, research, and content extraction" under Documentation in
  1.14.4a1, Features in 1.14.4). Click through
  `https://github.com/crewAIInc/crewAI/releases/tag/1.14.4` once and
  confirm the wording matches before quoting it on the call.
- **n8n integration URL.** `proposal/MAIN.md:43` was rewritten to point
  at `https://you.com/docs/integrations` after the original
  `docs.n8n.io/integrations/builtin/credentials/youcom/` 404'd.
  Eyeball the index page once and confirm n8n is listed; if the n8n-
  specific page is back up, swap the link.

### Live API smoke tests (COMPLETED 2026-05-16)

Both forks were exercised end-to-end against the live You.com Search
API across English, Arabic, and Japanese. **Six live calls, all 200,
all returned locale-appropriate results from regional domains.** Full
report and methodology in `research/e2e-verification.md`; raw captured
responses (parsed-output shape) in
`research/fixtures/e2e-live-2026-05-16/`.

Headline numbers:

- gpt-researcher: 6/6 unit tests pass + 3/3 live locales pass (5
  results each, EN/AR/JA, latencies 961–1326 ms)
- camel: 5/5 unit tests pass + 3/3 live locales pass (5 results each,
  EN/AR/JA, latencies 562–631 ms)
- Arabic native-script chars in returned content: 442 (camel), 596
  (gpt-researcher), across 5 results each
- Japanese native-script chars in returned content: 339 (camel), 820
  (gpt-researcher), across 5 results each
- Regional-domain hits: 1/5 (Arabic, both forks), 3/5 (Japanese,
  both forks)

Total live API spend across the project (Wave 1.4 + 2 E2E rounds):
13 calls, ~$0.065 of the $100 free signup credit.

The multi-region story in `proposal/MAIN.md` §4 is now backed by
captured live responses, not theoretical claims.

### Push-to-org status (DONE 2026-05-16)

Both forks pushed to Cooperation-org as **real GitHub forks** (verified
`fork: true`, parents `camel-ai/camel` and `assafelovic/gpt-researcher`).
Fork-internal review PRs are open:

- https://github.com/Cooperation-org/camel/pull/1 (`feat/youcom-search` → `master`, 8 commits)
- https://github.com/Cooperation-org/gpt-researcher/pull/1 (`feat/youcom-retriever` → `main`, 9 commits)

Both PRs follow Golda's spec exactly: PR from a feature branch in our
fork into the default branch in our fork — NOT against upstream. The
patches in `patches/` remain as a backup re-application path. The
forks themselves stay in sync with upstream by leaving each fork's
default branch untouched. Recommendation: have Gitonga pull from
the org URL during her dry-run rather than from a local working tree,
so Gitonga can pull from the org URL during her dry-run rather than
from a local working tree. Do NOT push to upstream `camel-ai/camel`
or `assafelovic/gpt-researcher` — opening upstream PRs is explicitly
gated on You.com sign-off (`call-kit/talking-points.md` "Hard rules"
and `proposal/MAIN.md` section 3 frame both forks as unpushed).

### "Eddy" Slack DM to Golda

One line, sent before Saturday evening:

> "Quick check before Monday — by 'eddy' you meant Eddy Nassif's eval
> harness, right? Just want to confirm before we cite him by name on
> the call."

If she confirms, the talking-points language about "Eddy Nassif's eval
harness covers the LangChain integration" is safe to use. If she says
"no, I meant <other name>," update `call-kit/qa-prep.md` Q9 and Q&A
references before the call.

### API key rotation after the demo

The current `YOU_API_KEY` was pasted into the working chat during the
build pass. After Monday's call (regardless of how it goes), rotate
the key and update any `.env` files in:

- https://github.com/Cooperation-org/camel (no `.env` shipped, but check)
- https://github.com/Cooperation-org/gpt-researcher (no `.env` shipped, but check)
- Any local shell profiles that exported the key

Treat the in-chat key as compromised by default. Cost of rotation is
zero; cost of leaving it is unbounded.

### Sync with Amr to confirm no doc duplication

Amr is the other person on the engagement. Confirm Sunday that he is
not preparing a parallel proposal doc that contradicts
`proposal/MAIN.md`. If he has follow-up material (slides, one-pager,
etc.), that's additive — but two competing 350-line proposals would
be embarrassing on a 10-minute call. One-line Slack to Amr is enough.

---

## Sunday-to-Monday timeline

| When | What | Who |
|---|---|---|
| Sat 2026-05-16 evening | Golda receives the Slack handoff above; begins review of `proposal/MAIN.md`, `call-kit/`, and the open-questions table below | Golda |
| Sun 2026-05-17 morning | Muhammad runs the Arabic + Japanese live smoke tests; Golda's edits land in the working repo | Muhammad + Golda |
| Sun 2026-05-17 afternoon | Push-to-Cooperation-org decision finalized; if green, push both forks; rotate the in-chat API key | Muhammad |
| Sun 2026-05-17 afternoon/evening | Gitonga receives a 30-minute walkthrough — proposal doc + demo script + a dry-run of section 3.3 (the gpt-researcher live test) | Muhammad + Gitonga |
| Sun 2026-05-17 evening | Final no-go check: any open P0 from this preflight unresolved? | Muhammad |
| Mon 2026-05-18 9:30 AM PT | Cooperation.org team on Slack, screen-share permissions confirmed, `YOU_API_KEY` exported in shell env (if section 4 of demo script will be used) | Team |
| Mon 2026-05-18 9:55 AM PT | Gitonga in call lobby, second-monitor docs open, screen-share preview tested | Gitonga |
| Mon 2026-05-18 10:00 AM PT | Call starts | All |

---

## Open questions for Golda's review (consolidated)

Pulled from `research/camel-build-report.md` section 6,
`research/gpt-researcher-build-report.md` section "Open questions for
human review," and `research/link-audit.md` sections 2, 3, 6.

Severity rubric: **P0** = blocks the call. **P1** = changes what we
say or what we ship. **P2** = follow-up after the call, no impact on
Monday.

| # | Question | Source | Severity | Recommended decision |
|---|---|---|---|---|
| 1 | Confirm "Eddy" = Eddy Nassif (Sr Applied Scientist, eval-harness lead) before citing him by name on the call | `research/eddy-investigation.md` §4; `research/link-audit.md` §6 | P1 | Send the one-line Slack DM. Default-yes; only flip if Golda objects. |
| 2 | Add `YDC_API_KEY` env-var alias (LangChain-compatible) to camel-fork's `search_you()` for migration parity, or ship `YOU_API_KEY`-only? | `research/camel-build-report.md` §6 item 1 | P2 | Ship `YOU_API_KEY`-only for now. Aliasing is a follow-up commit if user demand surfaces post-PR. |
| 3 | Add `safesearch` / `freshness` / `include_domains` to camel-fork's `search_you()` surface, or hold to the locked minimal surface? | `research/camel-build-report.md` §6 item 3 | P2 | Hold to minimal. Easy follow-up. Not in the proposal scope. |
| 4 | Defensive `.get("news", [])` merge for `results.news[]` handling in camel-fork? | `research/camel-build-report.md` §6 item 4 | P2 | Defer until news/livecrawl support is requested by a user. |
| 5 | Pre-existing `RUF059` lint warning on `test_search_functions.py:727` (Bocha test, unrelated to our additions) — fix in our PR or upstream separately? | `research/camel-build-report.md` §6 item 5 | P2 | Leave alone. Not our regression. Upstream cleanup PR if anyone cares. |
| 6 | Add `YOU_API_KEY=`/`YOU_COUNTRY=`/`YOU_LANGUAGE=` lines to gpt-researcher-fork's `.env.example`? | `research/gpt-researcher-build-report.md` §"Open questions" item 2 | P2 | Add as a follow-up commit before opening the upstream PR. Three lines. |
| 7 | Extend gpt-researcher's `topic` argument to switch between Search API and Research API (`topic="research"` posts to `https://api.you.com/v1/research`)? | `research/gpt-researcher-build-report.md` §"Open questions" item 3 | P2 | Out of scope for this engagement. Natural next iteration; flag in section 5.3 of the proposal as a Tier 2 commitment. |
| 8 | Pre-PR review with You.com DevRel before opening upstream PRs on either fork? | `proposal/MAIN.md` §5.2 (the ask itself) | P1 | This is one of the three things we're asking You.com for. Frame as a defaulted yes; if You.com declines, we still open the PRs but with less coordination. |
| 9 | Push to Cooperation-org GitHub before or after the call? | This preflight, "What Muhammad MUST do" §"Push-to-org decision" | P1 | Push before, after Golda's sign-off. Lets Gitonga pull from the org URL for her dry-run. |
| 10 | Live Arabic / Japanese smoke tests — surface captured responses to Golda before Monday, or just confirm "they returned 200" in Slack? | This preflight, "Live API smoke tests" | P1 | Send Golda the redacted captures Sunday afternoon. They are the strongest possible support for the multi-region story in §4 of the proposal. |
| 11 | DSPy 3.x dropped the `dspy/retrieve/` directory — flag the "candidate task" parenthetical in `proposal/MAIN.md:39` more prominently, or leave as-is? | `research/link-audit.md` §2 (DSPy link fix) | P2 | Leave as-is. The current "re-introduction is a candidate task" wording is honest. If You.com asks "is DSPy still covered" on the call, the answer is in `call-kit/qa-prep.md` Q15. |
| 12 | The CrewAI `you-contents` schema-patch claim ("~50 lines of user-side schema patching") — re-verify against any 1.14.5+ that may have shipped a fix? | `research/link-audit.md` §6; `research/crewai-verification.md` §2c | P2 | Eyeball the latest tag once before Monday. If CrewAI fixed the schema mismatch, update Q8 in `call-kit/qa-prep.md` to reflect that. |
| 13 | Tier 2 / Tier 3 star counts in `proposal/MAIN.md:107-120` — refresh the morning of the call, or accept the 2026-05-15 numbers? | `research/link-audit.md` §6 | P2 | Spot-check camel-ai and gpt-researcher Monday morning. The talking-points doc already says "round to 'around thirteen thousand'" so the rounded numbers are durable. |
| 14 | n8n integration URL in `proposal/MAIN.md:43` — was rewritten after the original 404'd; confirm `https://you.com/docs/integrations` lists n8n cleanly | `research/link-audit.md` §2; this preflight, "Manual verifications" | P2 | One eyeball pass. If the index doesn't list n8n by name, downgrade the row's "Source of record" further. |

---

## What we did NOT do

Honest non-goals and deferred items. Surface these to Golda so nothing
shows up as a surprise on Monday or in the post-call retro.

- ~~**Did NOT push to Cooperation-org GitHub.** Both forks are local-only.~~
  **Done 2026-05-16:** both forks pushed, fork-internal PRs opened
  (https://github.com/Cooperation-org/camel/pull/1,
  https://github.com/Cooperation-org/gpt-researcher/pull/1).
- **Did NOT open upstream PRs** on `camel-ai/camel` or
  `assafelovic/gpt-researcher`. Explicitly gated on You.com
  acknowledgement of the approach (`proposal/MAIN.md` §3, §5.1;
  `call-kit/talking-points.md` "Hard rules").
- ~~**Did NOT run live API smoke tests for Arabic or Japanese.**~~
  **Done 2026-05-16:** all 6 live calls passed across both forks for
  EN/AR/JA. Captured responses in
  `research/fixtures/e2e-live-2026-05-16/`; report in
  `research/e2e-verification.md`.
- ~~**Did NOT run camel-fork's full test suite end-to-end.**~~
  **Done 2026-05-16:** installed minimal deps (`openai mcp pydantic
  docstring_parser colorama pyyaml pillow`), camel `SearchToolkit`
  imports, and our 5 `search_you` tests pass via `pytest test/toolkits/test_search_functions.py -k you`
  (5 passed, 37 deselected, 2.45s). Plus 3 live locale calls — see
  `research/e2e-verification.md`.
- **Did NOT add `YDC_API_KEY` env-var alias** to camel-fork's
  `search_you()` (LangChain-compatible name). See open question #2.
- **Did NOT add `safesearch` / `freshness` / `include_domains`** to
  camel-fork's surface. See open question #3.
- **Did NOT add `topic="research"` branch** to gpt-researcher-fork
  (would route to `https://api.you.com/v1/research`). See open
  question #7.
- **Did NOT add `.env.example` entries** to gpt-researcher-fork. See
  open question #6.
- **Did NOT verify** the `@npaka123` X handle, the two LinkedIn
  profiles, the Eddy Nassif identification chain, the CrewAI 1.14.4
  release wording, or the n8n integration URL via human eyeball. See
  "Manual verifications" above.
- **Did NOT rotate the `YOU_API_KEY`** that was pasted in chat during
  the build pass. Muhammad's post-call task; see "API key rotation"
  above.
- **Did NOT modify any existing doc in the working repo** during this
  preflight pass — the link-audit and earlier waves already handled
  the fixes. This preflight is additive only (`patches/` directory
  and `preflight.md`).
- **Did NOT generate fallback screenshots** for `call-kit/screenshots/`
  per the demo-script's pre-flight checklist (§0.5). Those need to be
  taken on the actual demo machine with the actual editor / terminal
  layout — agent-side fabrication would be wrong. Muhammad or Gitonga
  takes them as part of the Sunday dry-run.
