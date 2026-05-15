# Cross-Region Outreach Playbook

Applies to any future region (LATAM, SEA, India, Korea, Francophone Africa). Distilled from the Arabic (LIVE) and Japanese (NEXT) engagements.

## 1. Repo selection criteria

A target repo must hit all three:

- **Active maintenance** — last commit in the past 90 days, or a clear release cadence.
- **Pluggable search abstraction** — an existing `BaseTool` / `Retriever` / `SearchProvider` interface with at least one implementation we can mirror. If the repo hard-codes a single backend, the PR becomes a refactor argument and the diff explodes.
- **Regional community ties** — the repo's maintainers or contributor base have a verifiable connection to the target region. "Written by someone in country X" isn't enough; we need someone whose audience is in country X.

## 2. Outreach order

Inverting this is the most common failure mode.

1. **Code first.** PR or fork lands before any content is written. Without a working artifact, every downstream message reads as marketing.
2. **Content second.** Blog post or notebook that uses the merged code. Native-language version goes through a paid translator.
3. **Community broadcast last.** Discord, X, regional subs get the link to the merged code plus the translated content. The community message is a pointer, not the artifact.

## 3. Translation policy

- **PRs and code comments: English, always.** Maintainers in every region we've researched (JP, AR, KO, ZH academic) review code in English.
- **Native-language content: paid translator with native review.** Budget $200–400 per piece. Translator drafts, separate native technical reader proofreads. Two people, two passes.
- **Never machine-translate outreach DMs or social posts.** Native speakers identify these in the first sentence and silently downrate the sender.

## 4. Eval angle

The single best reason for a regional contributor to engage is a **language-specific eval suite**. Not a feature ask, not a free API key — an eval. It is portfolio-credible, forces us to confront real locale failures, and gives the contributor something to co-author rather than consume. Lead every 1:1 with an eval invitation; the API-credits offer is a sweetener attached to it, not the headline.

## 5. Anti-patterns

- **Machine-translated outreach** in any channel.
- **Drive-by PRs without follow-up.** Budget review-response time before opening.
- **Claiming endorsement we haven't earned.** "Endorsed by camel-ai" requires a written quote. "Used by camel-ai" requires the merged PR. Know which sentence applies.
- **English-language posts in non-English forums.** If we don't have the translation ready, don't post.
- **Cold-emailing universities.** Low conversion, high noise. Go through a workshop submission, a co-authored eval, or a shared collaborator instead.

## 6. Success metrics

Within 30 days of the PR being merged:

- The PR is merged (binary).
- **At least one** community follow-up: an issue from a non-team contributor, a discussion reply, a reshare of the content piece, or a downstream PR using the integration.

Both required. A merged PR with no community engagement is a code contribution, not an ecosystem win — report it as such.
