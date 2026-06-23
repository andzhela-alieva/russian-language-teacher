# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Two parallel products for a Russian language teacher:
1. **Landing page** (`landing-page/`) — teacher's personal site (in progress; only `photo.jpg` exists so far)
2. **Interactive tasks** (`interactive-tasks/`) — standalone HTML exercises for students, opened directly in a browser

No build tools, no dependencies, no server. Every file is self-contained static HTML.

## Running / previewing

Open any `.html` file directly in a browser. No server needed. `feedback-sreens/` contains screenshots of student feedback used as design reference.

## Visual design system

All interactive tasks share a **"lined school notebook"** aesthetic. The canonical reference is **`interactive-tasks/DESIGN.md`** — it contains exact CSS values, component rules, and a list of deviations to fix. Always consult it when creating or editing a task file.

Key rules (full detail in DESIGN.md):

**CSS custom properties (always use these, never hardcode colours):**
```css
--paper: #f3f1e8; --rule: #c9d6e3; --margin: #e2a3a0;
--ink: #233047;   --ink-soft: #4b5a73;
--red: #b5392f;   --red-dark: #8c2a22; --green: #3f7a4f; --green-dark: #2d5e3a;
--pencil: #665f56; --gold: #b9912f;    --blue: #2c5f8a;
```

**Typography:** body `'PT Serif'`, h1 `'Caveat'` 700 40px, labels/numbers `'Courier Prime'`. All three loaded from Google Fonts.

**Layout:** `.page` max-width **760px**, padding `28px 28px 50px 64px`, `.margin-line` at `left: 42px`. Body background: `repeating-linear-gradient` ruled lines every 34px.

## Interactive task architecture

Each task file is one HTML page with all CSS inline in `<style>` and all logic in a single `<script>`.

**Question data model:**
```js
const questions = [
  { type: 'punct',     q: "...", points: N },          // punctuation placement
  { type: 'mcq',      q: "...", opts: [...], correct: N, points: 1, fb: {ok, bad} },
  { type: 'match',    q: "...", items: [{phrase, correct},...], points: N },
  { type: 'transform',q: "...", items: [{phrase, fromType, toType, sample},...], points: N }
];
```

**State:** `current` (index), `totalScore`, `answered[]`, `earned[]` — all module-level. `renderQuestion()` dispatches to `renderPunct / renderMcq / renderMatch / renderTransform`.

**Question types:**
- `punct` — text contains `{{id}}` placeholders compiled into clickable `.slot` spans; `slotMap` maps each id to `true` (comma needed) or `false` (decoy). Scoring: `earned = correctCount - floor(wrongCount/2)`.
- `mcq` — standard single-choice; immediate feedback via `.fb.ok / .fb.bad / .fb.mid` div.
- `match` — phrase ↔ connection-type buttons (`Согласование / Управление / Примыкание`); one "Check" button reveals all at once.
- `transform` — open text input, student self-grades after revealing the sample answer; `selfGrade` boolean stored on each item.

**Progress indicator:** Row of `.clip` circles at the top; `.done` (green) = answered correctly / earned points, `.miss` (red) = answered with zero score, `.active` (red ring) = current.

**Result screen:** Hidden `.result` card with `.stamp` circle showing grade 2–5, revealed after the last question.

## Content language

All user-facing text is in Russian. Keep all new task content, labels, feedback, and UI strings in Russian.
