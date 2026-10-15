# Arithmetic Tester

A Zetamac-style mental-arithmetic trainer. Single-page, offline-capable, built
to run as a home-screen PWA on iPhone. Timed run (default 120s); problems
auto-advance the instant your typed answer is correct, then a results screen.

No build step, no frameworks, no external/CDN libraries — deliberately, so it
keeps working fully offline.

## Files

- **index.html** — the entire app: inline CSS + vanilla JS, self-contained.
- **sw.js** — service worker, stale-while-revalidate caching. Registers only
  over HTTPS and must sit in the same folder as `index.html`.

## How it works

- **Answer entry** uses the native numeric keyboard (`inputmode="numeric"`).
- **Advance mode** — configurable per preset (`Auto` vs `Enter`):
  - `Auto`: advances the instant your typed answer is correct.
  - `Enter`: requires pressing Enter to submit; incorrect answers move immediately to the next question and are cataloged as mistakes on the results screen.
- **Question presentation & speech** — configurable per preset (`Text`, `Both`, `Spoken`):
  - `Text`: standard visual text display.
  - `Both`: question is written on screen and spoken aloud using the native Web Speech API (`SpeechSynthesis`).
  - `Spoken`: question is spoken aloud only (hidden on screen during play; actual problems remain fully recorded on the results screen).
  - **Repeat**: a "Repeat" button allows replaying the current question, also mapped to the numpad `+` key (`NumpadAdd`).
- **Presets** — 6 modes live in the `PRESETS` array at the top of the script in
  `index.html`, displayed as a 2-column by 3-row grid:
  - Row 1: `Default (Text)` & `Default (Speech)` (Zetamac defaults +−×÷, 120s)
  - Row 2: `Mult / Div (Text)` & `Mult / Div (Speech)` (12–19 × 12–100, 10 q)
  - Row 3: `3-digit (Text)` & `3-digit (Speech)` (3-digit add/sub, 120s)
  Pick on the home screen; Start runs the selected one.
- **Run mode** — each preset ends either on a time countdown (`mode:'time'`,
  `dur` seconds) or after a fixed number of questions (`mode:'count'`, `count`
  questions, timer counts up). Default and 3-digit presets are timed; Mult/Div
  defaults to a question count. Toggle it per preset in the settings sheet.
- **Settings cog** edits the selected preset in memory for the session only —
  changes reset on reload. Permanent changes mean editing the `PRESETS` array.
- **Math invariants** — subtraction is always non-negative (rendered as
  `(a+b) − a = b`); division is always integer (`a*b ÷ a = b`).
- **Stop button** (top-right during a run) ends early; the per-minute rate is
  computed from actual elapsed time, not the full duration.
- **Results screen** — a hand-rolled SVG timeseries (seconds per question
  across the run, with a dashed average line) plus a "slowest questions" list,
  mistakes list (if in Enter mode), and recent runs for that preset.
- **Local score history & Personal Bests** — runs are persisted per preset in
  the browser's `localStorage` (`trainer_history_v1`), keeping the app 100%
  offline and private:
  - Personal Best (PB) and run count are displayed directly on preset cards.
  - A top-bar graph button (and results panel) opens the History sheet to
    inspect stats (PB, average speed, run count) and scroll through past runs.
  - History can be cleared per preset if desired.

## Deploy

Static hosting over HTTPS (GitHub Pages / Netlify). Keep both files in the same
directory. After changes, bump `CACHE` in `sw.js` (`trainer-v6` → `v7` …) and
reload once online so the new service worker activates — otherwise cached
versions keep serving.
