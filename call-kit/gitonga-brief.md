# Gitonga's Pre-Call Brief

**Call:** You.com pitch
**When:** Monday 2026-05-18, 10:00 AM PT
**Length:** 10 minutes
**Reader:** Gitonga Miriam (you)

This brief is written for you, not for a developer. You can do
everything on this page without opening a terminal or editing code.
Print it or open it on a second screen during the call.

---

## What this project actually is

We added You.com's search to two open-source AI projects that did not
have it yet. The proof is two pull requests on GitHub. We also
captured live search results in three languages (English, Arabic,
Japanese) on Saturday to show it really works. The whole pitch on
Monday is showing You.com what we built, and asking them to back us
to do more of the same.

---

## Your job on the call

Ten minutes. Stay on the clock. The shape:

- **0:00 to 1:00** -- Opening. Read the 60-second pitch from
  `call-kit/talking-points.md`. Look at the camera, not the page.

- **1:00 to 1:30** -- Share your screen. Bring up the demo page (see
  next section for how to launch it). Press F11 to go full-screen.

- **1:30 to 3:00** -- Scroll to the section called "What we shipped".
  Two cards side by side. Point at each one. Say "this is the
  camel-ai fork, eight commits, all tests passing" and "this is the
  gpt-researcher fork, nine commits, all tests passing". Hover over
  the "View PR" buttons so You.com sees the links exist.

- **3:00 to 5:30** -- Scroll to "Multilingual proof". Click the
  English tab first. Read one title aloud. Click the Arabic tab. Say
  "this is a real query, a real response, the country was set to
  Saudi Arabia and the language to Arabic". Read the top result's
  title out loud if you can; if not, just say "five Arabic results
  from Arabic-language domains". Do the same for the Japanese tab:
  "country JP, language ja, five results, three from Japanese
  domains". This is the strongest visual on the page. Slow down here.

- **5:30 to 6:30** -- Scroll to "What we'd ship next". Point at the
  Tier 2 list (six repos) and say "this is the next engagement's
  commitment list, in priority order".

- **6:30 to 9:30** -- Questions. If You.com asks something you can
  handle from the "Anticipated questions" section on the demo page
  (or from `call-kit/qa-prep.md`), answer it. If they ask anything
  about pricing or contracts, defer to Golda. If they ask anything
  deeply technical, use the defer template below.

- **9:30 to 10:00** -- Close. Say "we have a working repo, two open
  PRs, and the multilingual responses captured. Happy to send the
  links in a follow-up email today." Then thank them and end the
  call.

If the call runs short, you can skip "What we'd ship next" and go
straight to questions.

---

## How to launch the demo page

Three steps. Do this in front of your own face before the call so
you know it works.

1. Open File Explorer (the yellow folder icon on your taskbar).
2. In the address bar at the top, paste this and press Enter:
   `E:\youdotcom\workspace\youcom-ecosystem-work\demos\`
3. Double-click the file called `index.html`. It opens in your
   default browser. Press F11 to go full-screen for screen-share.

The page works without internet. The data is built in. If your wifi
drops mid-call, the page still looks the same.

To go back from full-screen, press F11 again.

To switch between the three language tabs in the "Multilingual proof"
section, just click them with your mouse. The buttons say "English",
the Arabic word, and the Japanese word.

---

## Three browser tabs to have open before the call

Open these three tabs in your browser before you dial in, in this
order, left to right:

1. **Tab 1: the demo page.** Open the file from the steps above.
   This is your main visual.
2. **Tab 2: the working repo.** Go to
   `https://github.com/dev-mhany/youcom-ecosystem-work` in your
   browser. This is where all our code and notes live. You might
   show it if someone asks to see the repo itself.
3. **Tab 3: one PR page.** Your pick. Either
   `https://github.com/Cooperation-org/camel/pull/1` or
   `https://github.com/Cooperation-org/gpt-researcher/pull/1`.
   Pick whichever loads faster for you. If someone asks "can we see
   one of the PRs", this is the tab.

Do not have anything else open. Close email, close Slack, close
notes. The fewer tabs, the less chance you click the wrong one on
share.

---

## If they ask a technical question you can't answer

Use this exact line. Three variations, pick the one that fits:

- **If Muhammad or the tech lead is on the thread today:**
  "Good question -- I want Muhammad to weigh in on the
  implementation specifics. Can he jump in on the chat now, or
  shall I follow up by email later today?"

- **If you are alone on the call:**
  "Good question -- I want our tech lead to answer that one
  properly rather than guess. I will follow up by email today with
  the answer."

- **If they push for an answer in the moment:**
  "I would rather give you the right answer in writing than the
  wrong answer now. Today's email will have it."

Do not guess. Do not try to half-answer. The cost of saying "I'll
follow up" is zero. The cost of an inaccurate answer to a developer
audience is real.

---

## Glossary -- five terms that might come up

- **Fork** -- a copy of someone else's GitHub project that you can
  edit. The original stays untouched. Ours are copies of two AI
  projects that we added You.com search into.

- **PR (Pull Request)** -- a formal request to merge your changes
  into another project. It is a webpage on GitHub that shows
  exactly what changed, line by line. Reviewers comment on it,
  approve it, and either merge it in or reject it.

- **Repo (Repository)** -- a project's home on GitHub. Contains
  all the code, all the history, all the discussion. The word
  "repo" is short for "repository".

- **API key** -- a long password-like string that lets a piece of
  code talk to a service like You.com. Think of it as the badge
  the code wears to prove it is allowed in.

- **Branch** -- a named line of changes inside a repo. You can have
  many branches in the same repo, all worked on in parallel. Our
  fork branches are named `feat/youcom-search` and
  `feat/youcom-retriever`.

---

## The 5 things you must NOT say

These are the easy ways to lose credibility with a developer
audience. Internalize them before the call.

1. **Do not say CrewAI added a You.com integration as Python code.**
   They added documentation only. The actual integration runs on
   You.com's side. Say "CrewAI ships documentation" -- never "CrewAI
   added an integration".

2. **Do not credit Eddy by name for the LangChain integration.**
   Three other people wrote it (sam-h-bean, scottnath, christeefy).
   Eddy Nassif leads a different thing -- the eval harness. Only
   say his name if someone asks about evaluation methodology.

3. **Do not say the forks are merged into the original projects.**
   They are not. They are open pull requests under our company
   org, waiting for You.com's green light before we ask the
   original maintainers.

4. **Do not use marketing words.** No "exciting", no "thrilled", no
   "robust", no "leverage", no "best-in-class", no "game-changer",
   no "supercharge". You.com's audience is engineers. Plain words
   land. Marketing words read as fluff. The full banned list is in
   `talking-points.md`.

5. **Do not invent numbers or quote anyone we haven't checked
   with.** Star counts are rounded to "around thirteen thousand",
   not "12,847". Latency is "about one second", not "1326
   milliseconds". If you don't remember a number, say "I have it
   in the page, let me find it" and look.

---

## Pre-call checklist (15 minutes before)

Do these in order, the morning of:

- [ ] Close Slack, email, calendar pop-ups. Set Do Not Disturb.
- [ ] Open File Explorer to
      `E:\youdotcom\workspace\youcom-ecosystem-work\demos\` and
      double-click `index.html`. Confirm it opens in your browser.
- [ ] Press F11 to go full-screen on the demo page. Confirm the
      page fills the screen and looks right.
- [ ] Press F11 again to come back. Click the Arabic tab in the
      "Multilingual proof" section. Confirm Arabic characters
      display correctly (not as boxes). Same for Japanese.
- [ ] Open the working repo in a second tab:
      `https://github.com/dev-mhany/youcom-ecosystem-work`
- [ ] Open one PR in a third tab:
      `https://github.com/Cooperation-org/camel/pull/1`
- [ ] Test your microphone with whoever is on the team thread
      five minutes before the call. Confirm your camera works.

If anything in this list fails, message the team thread immediately.
Do not try to debug it yourself two minutes before the call.

---

## If everything fails

If the live demo dies, if your browser crashes, if your screen-share
freezes -- you still have a story.

The demo page is local and offline. It does not need internet, it
does not need an API key, it does not need a terminal. If the page
loads, you have the demo. If the page itself won't load:

- Walk through the proposal verbally. The 90-second version in
  `talking-points.md` covers all the key points without any visual.
- Tell them: "the page is a recap of what is already pushed to
  GitHub -- I can email it after the call so you can read at your
  pace." Then actually send it.
- If asked to show code right now and you cannot, say: "the
  fastest path is to open the PR on GitHub" and share the PR URL
  in chat. Let them open it themselves.

The whole pitch is held together by two real GitHub PRs and six
captured language responses, all of which exist independently of
your screen-share working. If your laptop catches fire, You.com can
still verify everything we said by clicking the three links in your
follow-up email.

You've got this. Keep it short, defer when in doubt, and trust the
page to do the heavy lifting.
