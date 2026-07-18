# PROID WEBSITE

## Project
Course project for PROID, Ngee Ann Polytechnic — Year 3, Semester 1.
Status: in progress. First deliverable (FIND RACHEL) is built.

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
- `index.html` — the entire site (structure, styles, and logic all inline).

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
