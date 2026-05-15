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

*To be filled.*

## 2. Proposed targets (uncovered)

*To be filled.*

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
