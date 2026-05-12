# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

Science Olympiad Division A practice tests for the "Operation: Reconstruction" event (Woodsbury 2026). Each test is a **single self-contained HTML file** with no build step, no dependencies, and no package manager. Open any `.html` file directly in a browser.

## Viewing / Testing

```bash
# Open in default browser (Linux)
xdg-open OpRecon_PracticeTest1_SpaceStation.html
xdg-open OpRecon_PracticeTest2_Robot.html

# Or serve locally to avoid any CORS issues
python3 -m http.server 8080
```

There are no lint scripts, no test suites, and no CI configuration.

## File Architecture

Each HTML file is a standalone interactive practice test with three layers embedded in order: CSS → HTML → JavaScript.

**HTML structure (each file):**
```
.hero           — gradient header with event title and badges
.container
  .tab-nav      — tab buttons (data-tab attribute drives switching)
  #tab-overview — event rules, materials list, timing table
  #tab-writer   — writer countdown timer + SVG object diagram + assembly guide
  #tab-builder  — builder countdown timer + material tray + self-assessment checklist
  #tab-scoring  — interactive scoring rubric with live running total
  #tab-tips     — strategy tips (Test 1) / advanced techniques (Test 2)
```

**JavaScript (end of each file) handles three things:**
1. **Tab switching** — removes/adds `.active` on `.tab-btn` and `.tab-panel` elements matched via `data-tab`
2. **Dual timers** — `timers` object with `writer` and `builder` keys; each stores `remaining` (seconds) and `interval` (setInterval ref); `startTimer`, `pauseTimer`, `resetTimer` functions; turns red at ≤60 s
3. **Scoring** — `pointMap`/`pm` object maps checkbox IDs (`s1`–`sN`) to point values; `calcScore()` sums checked boxes and updates `#totalScore`

## CSS Conventions

- All colors and shadows are defined as CSS custom properties on `:root` at the top of `<style>`.
- Each test uses its own distinct palette and font pairing (Test 1: Fredoka + Nunito / warm orange-teal; Test 2: Baloo 2 + Quicksand / blue-green).
- Card accent colors use modifier classes: `.card.accent`, `.card.purple`, `.card.gold` (Test 1) or `.card.green`, `.card.orange`, `.card.purple` (Test 2).
- Print layout is handled with `@media print` — tab nav, timers, and print button are hidden; all panels are forced `display: block`.
- Mobile breakpoint: `@media (max-width: 600px)`.

## Adding a New Practice Test

Follow the established pattern:
1. Copy an existing file as a starting point.
2. Update the `pointMap`/`pm` object — IDs must match the checkbox IDs in `#tab-scoring`, and the denominator in `#totalScore` must match the sum of all values.
3. Replace the SVG inside `.object-display` with an inline SVG diagram of the new object (labeled with `<text>` elements for clarity).
4. Update the assembly steps in the `.step-list` inside `#tab-writer`.
5. Choose a new CSS palette — update all `--` variables in `:root` and pick a matching Google Fonts pair in `<link>`.
6. Test print layout with `window.print()` before finalizing.

## Key Constraints

- **No drawings allowed** in the real event — the SVG diagrams are for the event supervisor/coach reference only (shown in the Writer tab's "Assembly Guide" section, not handed to students).
- The Builder intentionally receives decoy materials — the scoring rubric checks for incorrect piece usage (e.g., `s20` in Test 2: "No incorrect decoy pieces used").
- Timer state is not persisted — refreshing the page resets both timers.
