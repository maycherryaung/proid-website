# PROID WEBSITE

## Project
Course project for PROID, Ngee Ann Polytechnic — Year 3, Semester 1.
Status: in progress. Stage 1 (FIND RACHEL, `index.html`), Stage 2
(`zone2.html`), Stage 3 (`zone3.html`), and Stage 4 (`zone4.html`, the
final stage) are built.

## Purpose
"FIND RACHEL" — a single-page interactive privacy-awareness exhibit, and stage 1
of a larger multi-stage activity. The player explores a fake Instagram profile
for a fictional 19-year-old, finds four pieces of personal info hidden in
posts/comments/images, enters them as codes to open a locked box, and gets a
dossier reveal followed by a privacy-harm message. Solving it reveals a key
code needed to proceed to the next stage of the activity. No real person or
data — everything is fabricated for the exhibit. Demonstrates how easily a
stranger can be profiled from public social posts.

## Tech stack
Vanilla HTML/CSS/JS, single self-contained file. No frameworks, no build step, no
network calls, no external assets (icons are inline SVG).

## Commands
None — open `index.html` directly in a browser. No build/test tooling.

## Files
- `index.html` — Stage 1, "FIND RACHEL" (structure, styles, and logic all inline).
- `zone2.html` — Stage 2, the "spot the fake screenshot" forensic exhibit
  (also fully self-contained; duplicates rather than shares CSS/JS with
  `index.html`, since each zone file is independently self-contained by
  design — no shared stylesheet/script between zones).
- `zone3.html` — Stage 3, the real Instagram-report + OSC-escalation
  walkthrough (same independently-self-contained convention).
- `zone4.html` — Stage 4, the final stage: writing a message to Rachel,
  which joins a persistent public message wall (same convention; this one
  uses `localStorage` for the wall data, the only zone that persists
  anything beyond a single session).

## Git workflow
- Repo: private GitHub repo `maycherryaung/proid-website`, remote `origin`,
  default branch `main`.
- **Standing instruction: when the user says "commit to GitHub" (or a clear
  variant of that phrase) in this project, stage all changes, write a
  concise commit message describing what changed, commit, and `git push` to
  `origin main` — without asking for confirmation first.** This is
  pre-authorized here per the user's explicit request, overriding the
  normal default of confirming before every push.

## Notes
- Look: `#0A0A0A` background, `#FFFFFF`/`#8E8E93` text, single `#FF3B30` accent,
  `#2A2A2A` 1px borders, sharp corners (4px max), system font + monospace for
  timer/codes/dossier.
- Lock answers (case-insensitive, whitespace-stripped): LOCK 1 school
  ("ngee ann"/"ngee ann polytechnic"/"np"), LOCK 2 home ("312"/"0312"), LOCK 3
  "what is the name of her dog?" ("momo"), LOCK 4 "when her house is empty" —
  DDMM format, "2111" only (no "21 nov" variant; input placeholder reads
  "DDMM", maxlength 4).
- Closing sequence line 3 opens with "Online stalking is one of the five harms
  the Online Safety Commission opened with on 29 June 2026." — the "Online
  Safety Commission" / "five harms" / "29 June 2026" portion was reconstructed
  from a verbal brief rather than a pasted source doc; double-check exact
  wording against the original brief if this needs to be precise for a
  citation/footnote in the assignment writeup. Line 5 ("Now do it to
  yourself. See what information you're giving away.") and the exit-note
  below the printable blank dossier ("This dossier will be given to you to do
  this exercise on yourself at home. Please collect it at the exit station.")
  are exact as given.
- The brief screen (before START) now lists the four lock questions verbatim,
  so players know what to hunt for before they ever open the profile — keep
  this list in sync with the `.lock-label` text in the box screen if the
  questions change again. Box↔profile navigation (OPEN THE BOX / BACK) is a
  pure screen-toggle and already preserves in-progress lock input text and
  solved state — no reset happens on that path. The box screen's BACK button
  is pinned (`position:fixed`, below the timer bar) so it's reachable at any
  scroll position, matching the profile's fixed OPEN THE BOX button.
- The dossier → closing-message transition used to be an automatic 4.5s
  timeout; it's now a manual `CONTINUE` button on the dossier, since an
  unattended-exhibit player could walk away before the timeout fired and
  never see the actual privacy-harm message. Don't reintroduce an
  auto-timeout there.
- The closing screen no longer ends on `RUN IT AGAIN` — it ends on a
  `GET THE KEY FOR THE NEXT STAGE` box. Clicking it reveals a new key screen
  (`#password-view`) showing the stage key **"XABCGH"**, a live "Resets in
  M:SS" countdown starting at 1:00, and `RUN IT AGAIN` (moved here). If the
  countdown hits 0:00 with no click, it auto-resets the whole exhibit back to
  the brief screen via the same `resetAll()` path — this is the kiosk's
  self-reset failsafe for an unstaffed exhibit, confirmed working via a real
  60-second timed CDP test. If the next-stage key value ever changes, it's
  the `.key-value` text in `#password-view`.
- Verified end-to-end via a headless-Chrome CDP smoke test (full happy path +
  wrong-answer + reset): no console errors, all screens/transitions worked.
- Real photos were intentionally left as text-labelled placeholder blocks
  (`#1A1A1A` block, `#3A3A3A` label chip) — swap in real `<img>` later without
  restructuring markup.

## Zone 2 (`zone2.html`) notes
- Gated behind Stage 1's key: `#screen-gate` requires **"XABCGH"**
  (case-insensitive/trimmed, same `normalize()` pattern as the Stage 1
  locks) before it lets the player through to the brief screen. Wrong entry
  reuses the exact `.lock.shake` shake-and-clear pattern from `index.html`.
- The "spot the fake screenshot" hunt has 3 hotspots, each a real (invisible
  until `.found`) element layered over the actual content it's about — no
  manual hit-testing math. Fixed letter assignment regardless of find order:
  hotspot-1 (DM timestamp `11:47 PM` vs status bar `9:12`) → **L**,
  hotspot-2 (`1,204 followers` — matches Rachel's real, current count from
  `index.html`, contradicting the post's "two years ago" claim, checkable via
  the KNOWN FACTS panel) → **I**, hotspot-3 (uneven letter-spacing on
  "Rachel Tan" in the DM header, done via hand-tuned per-letter
  `letter-spacing`) → **E**. Assembles to the evidence code **LIE**, shown
  large (`clamp(2.5rem,22vw,200px)`) on the payoff screen.
- Zoom/pan on the screenshot is hand-rolled with the Pointer Events API (no
  library): one active pointer drags/pans, two active pointers pinch-zoom by
  distance ratio, `wheel` zooms on desktop. Scale is clamped `[1,3]`.
  `preventDefault()` only fires once a gesture's movement crosses a small
  threshold, so a genuine tap still falls through to a normal `click` on the
  hotspot underneath — this is *why* the hotspots don't need custom
  tap-vs-drag detection of their own.
- Applying the lesson learned on `index.html`: the "3/3 evidence found" →
  payoff transition is a manual `CONTINUE` button, not an auto-timeout, for
  the same unattended-exhibit reason.
- The payoff screen ends on the identical `GET THE KEY FOR THE NEXT STAGE` →
  key-screen → 60s countdown → `RUN IT AGAIN` mechanic as `index.html`,
  showing next-stage key **"TYWAJF"**. `RUN IT AGAIN` / countdown-expiry /
  `?reset` all reset zone2 back to its own `#screen-gate` — independent of
  `index.html`'s reset, since they're separate files/sessions.
- The 4 hostile comments intentionally reuse Rachel's own friends' handles
  from `index.html` (@jiaqi_, @danielsoh, @weiling.t, @nurulhda) — the same
  people supporting her in Stage 1 are shown piling on the fake accusation
  here, reinforcing "nobody checked."
- Verified end-to-end via the same headless-Chrome CDP pattern: gate
  wrong/right (both cases) password, all 3 hotspots (code assembles in fixed
  position order regardless of click order, re-clicking a found hotspot is a
  no-op), payoff text/code, key screen, and reset. No console errors.

## Zone 3 (`zone3.html`) notes
- Gated behind Stage 2's key: `#screen-gate` requires **"TYWAJF"**, same
  gate pattern as `zone2.html`.
- The Instagram report flow (`#screen-report`) is **plain static HTML**,
  one `.report-step` block per step (`#report-step-0` through
  `#report-step-6`), each option row a fixed-`id` `<div class="report-row">`
  wired individually in `DOMContentLoaded` — e.g. `#opt-report`,
  `#opt-something-account`, `#opt-content-type`, `#opt-harassment` are the
  4 rows that actually advance (matching the brief's exact path: Report →
  Something about this account → It's posting content that shouldn't be on
  Instagram → Harassment or bullying → Submit); every other row has no
  listener at all — genuinely inert, not just visually distinguished,
  consistent with this project's "wrong/other option = no feedback"
  convention. `showReportStep(n)` is just hide-all/show-`#report-step-n`.
  This *used* to be a data-driven renderer (`REPORT_STEPS` object +
  `document.createElement` + per-row `addEventListener` in a loop) — it was
  rewritten to fully static markup after a real user (on Microsoft Edge)
  repeatedly reported step 2 as unresponsive, which never reproduced in
  automated testing (including real synthesized `Input.dispatchMouseEvent`
  clicks, not just `.click()`). The dynamic-render approach was never
  conclusively proven to be the cause, but it was the *one* code path in
  the whole project that built DOM elements at runtime instead of using
  static HTML wired by fixed ID — every other screen in `index.html`,
  `zone2.html`, and the rest of `zone3.html` already used the static
  pattern successfully. Simplifying to match that removed the one
  outlier and the reported symptom did not recur after the rewrite.
  **If a future zone needs a repeated/generated list, prefer static
  per-item markup with fixed IDs over `createElement`-in-a-loop**, given
  this history.
- Category wording throughout the report flow is realistic Instagram
  report-flow terminology, authored (not copy-pasted from a source), for
  "faithful clone" authenticity. Step 2's header reads "Why are you
  reporting this post?" (the option label itself, "Something about this
  account," is unchanged — that phrase came verbatim from the brief's
  specified path, only the header question was mine to word).
- `.report-row`/`.report-back` have `cursor:pointer` plus a hover/active
  background change — without this they felt dead to a real user (no
  visual signal anything was interactive, even the row that actually
  advances), even though the click logic itself tested as correct. Don't
  ship a real-looking list/menu row in this project without cursor+hover
  affordance, even when most of its options are intentionally inert.
- A console error some browsers may log for local `file://` pages with
  `<input>` fields — "Unsafe attempt to load URL ... 'file:' URLs are
  treated as unique security origins" — was seen on Edge during this
  investigation and is a known benign artifact of the browser's own
  autofill/password-manager probing local files; it did not correlate
  with the actual bug and needed no fix. Don't chase it if seen again.
- The wait screen's 24-hour clock is genuinely time-based
  (`requestAnimationFrame` interpolating `00:00`→`24:00` over a fixed
  8000ms, not a fixed number of ticks), and there is deliberately no skip
  control anywhere in the markup. After hitting `24:00` it holds, then a
  real `setTimeout(3000)` "dead air" beat runs before the "Reviewed..."
  message and a `CONTINUE` button fade in (button staggered slightly after
  the text via `animation-delay`, not simultaneous) — verified by measuring
  actual elapsed wall-clock time in the CDP test (~11.1s total), not just
  checking the DOM state.
- The escalation screen's 5-item OSC list: tapping any item toggles its
  checkbox visually; only "Online harassment" (`data-correct="true"`)
  reveals `CONTINUE`; every other item shows "Not this one." in a shared
  feedback line with no penalty or attempt limit, matching the brief
  exactly ("reading the list IS the lesson").
- Payoff shows the closing lines, then a permanent (non-disappearing) OSC
  reference block rendered as plain static text — `osc.gov.sg` is NOT a
  real `<a href>`, consistent with the project's no-network-calls rule —
  then the same `GET THE KEY FOR THE NEXT STAGE` → key screen → 60s
  countdown → `RUN IT AGAIN` ending as the other zones, showing next-stage
  key **"UAPLOM"**. (There used to also be a `CODE: 2026` display between
  the closing lines and the OSC block — removed per user request; if a
  4-digit code ever needs to come back here, note the OSC block/key-box
  `animation-delay` values were shifted down by 1s each to close the gap,
  so re-adding it would need those bumped back up.)
- **Bug found and fixed during verification, worth remembering for any
  future zone file:** `#screen-wait` and `#screen-escalation` were
  initially given bare-ID CSS rules setting `display:flex` (e.g.
  `#screen-wait{display:flex;...}`). Because an ID selector's specificity
  beats `.screen.active`'s two-class specificity, those rules made both
  screens *permanently* visible regardless of the `.active` class — the
  screen-toggle mechanism silently stopped working for just those two
  screens. Screenshots (not just DOM-state assertions like
  `classList.contains('active')`, which still returned correctly) caught
  it. Fix: scope any screen-specific `display` override to the `.active`
  class, e.g. `#screen-wait.active{display:flex;...}` — never a bare
  `#screen-name{display:...}` rule.
- Verified end-to-end via the same headless-Chrome CDP pattern, including
  actually walking the correct report-flow path, confirming an inert
  option doesn't advance, measuring the wait screen's real elapsed time,
  exercising wrong-then-correct OSC picks, and reset. No console errors.

## Zone 4 (`zone4.html`) notes
- Final stage — gated behind Stage 3's key **"UAPLOM"**, same gate pattern
  as the other zones, but this one has **no ending key/next-stage screen**
  at all (confirmed with the user: since it's the last zone, it just ends
  on the message wall — no `.key-box`/`#password-view`/countdown
  machinery exists in this file, unlike `index.html`/`zone2.html`/
  `zone3.html`).
- Brief screen lines fade in at an exact 1.5s cadence (`animation-delay`
  `.3s` / `1.8s` / `3.3s`), distinct from the ~1-1.2s cadence used for the
  closing sequences in the other zones — this pacing was specified
  explicitly for this screen ("one line at a time, 1.5s apart").
- Compose screen (`#screen-compose`) is deliberately minimal per the brief:
  just a labelled textarea and one `SEND` button, disabled only while
  empty (`text.trim().length === 0`) — the original 15-character minimum
  was removed per user request after they found it confusing (a disabled
  button gives no visual reason why, so a too-short message just looked
  like "nothing happens"). No other UI exists (no counter/timer/progress/
  prompts/confirmation — none of that exists in the markup, not just
  hidden).
- **This is the only zone that persists data across sessions.** Messages
  live in `localStorage["zone4_messages"]` as a JSON array, seeded once
  (only if the key doesn't exist yet) with 30 authored placeholder
  messages so the wall is never empty on day one — several deliberately
  reference the earlier zones' narrative (Momo, jiaqi, "that post was
  fake", "that account got reported") for continuity. `resetSession()`
  (wired to `?reset`, and now also to the `END CHALLENGE` countdown
  expiry — see below) only clears the on-screen gate/compose state and
  returns to the gate screen — it never touches `localStorage`, so the
  wall accumulates for the whole festival as specified.
- Compose screen originally had a 15-character minimum before `SEND`
  enabled; removed per user request (a disabled button gives no visual
  reason why, so short messages just looked broken). `SEND` is now only
  disabled while the textarea is empty. There's also a `.compose-hint`
  line ("Send a message to reach out to her.") above the textarea, added
  after the user found the bare textarea+button confusing with zero
  context — this is a deliberate exception to the original brief's
  "no prompts" rule, per explicit user request.
- The wall screen has an `END CHALLENGE` button (`#btn-end-challenge`,
  inside `#wall-view`). Clicking it hides `#wall-view` and shows
  `#end-view`: "Thanks for participating." plus a live "Resets in M:SS"
  60-second countdown (`startEndChallenge()`), auto-calling
  `resetSession()` at 0:00 — same self-reset-for-the-next-player pattern
  as the other zones' key screens, just without a key (this is the last
  stage). No manual "reset now" button was added here, only the
  countdown — matches what was explicitly asked for. Verified with a
  real full 60-second wait (not simulated) that it actually auto-resets
  and that the message wall is preserved across that reset.
- The wall (`#screen-wall`) renders newest-first (reverse of storage
  order) via `renderWall()`, which recomputes the "You are the Nth person"
  line from `messages.length` every time — the count is real, not a fake
  incrementing display. The just-sent message gets an `.own` class (accent
  left border, fade-up entrance) that's removed via `setTimeout(10000)`,
  matching the "10 seconds then becomes anonymous like the rest" spec.
- Hidden admin export: a transparent 64×64px `.admin-hotzone` fixed to the
  top-right corner, present on every screen (outside the `.screen` toggle
  system), listens for 3 clicks within a 1.5s rolling window and then
  downloads all stored messages as `rachel-messages.txt` via a `Blob` +
  temporary `<a download>` (no network call — a local `blob:` URL, so this
  doesn't violate the no-network-calls rule). Verified by spying on
  `URL.createObjectURL`/`HTMLAnchorElement.prototype.click` directly
  (CDP's `Page.downloadWillBegin` event didn't reliably fire for this
  programmatic-Blob-download pattern in headless testing, so don't rely on
  that event if re-testing this — spy on the actual JS calls instead) —
  confirmed it fires only on the 3rd tap, not the first two, and doesn't
  double-fire on a stray 4th tap outside the window.
- Verified end-to-end via headless-Chrome CDP with real synthesized mouse
  clicks: gate wrong/right, brief stagger timing (checked opacity
  mid-sequence, not just at the end), SEND disabled/enabled boundary,
  send → wall transition with correct count and `.own` highlight/expiry,
  persistence across a full page reload, `?reset` confirmed to preserve
  the wall while resetting the session, and the triple-tap export. No
  console errors.
