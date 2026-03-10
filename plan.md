# Plan: Full Soc Ops Redesign — Dark Ops-Room Aesthetic

## TL;DR
Replace the flat gray-on-white design with a dark, professional "ops room" aesthetic. Fonts → Space Mono (headings/monospace elements) + DM Sans (body). Color → near-black base `#0a0f1a`, sky-blue `#0ea5e9` accent, dark greens for marked cells, amber-yellow for winning cells. Touches: dot-grid background on start screen, radial glow at top, subtle board glow, sharp modal animation.

---

## Files to Modify

| File | What changes |
|---|---|
| `app/static/css/app.css` | Full replacement — new token system, component styles, Google Fonts import |
| `app/templates/base.html` | Add `lang`, update theme-color meta to `#0a0f1a` |
| `app/templates/components/start_screen.html` | New semantic markup using new component classes |
| `app/templates/components/game_screen.html` | New header/hint/banner/board-wrapper markup |
| `app/templates/components/bingo_board.html` | Replace inline utility soup with `.bingo-grid` / `.board-cell` component classes |
| `app/templates/components/bingo_modal.html` | Replace with `.modal-overlay` / `.modal-card` markup |

`home.html`, `app/main.py`, `app/game_logic.py`, `app/game_service.py`, `app/models.py`, `app/data.py` — NO changes needed.

---

## Design Tokens (CSS variables in `:root`)

```
--bg:          #0a0f1a
--surface:     #111827
--surface-2:   #1a2235
--border:      #1e2d45
--border-hi:   #2a3f5f

--accent:      #0ea5e9     (sky-500)
--accent-dim:  #0284c7
--accent-glow: rgba(14,165,233,0.18)

--marked:        #0d2b1e   (dark green bg)
--marked-border: #14532d
--marked-text:   #4ade80
--marked-check:  #22c55e

--win:           #1a1a00   (dark amber bg)
--win-border:    #713f12
--win-text:      #fde047

--text-primary:   #f1f5f9
--text-secondary: #94a3b8
--text-muted:     #475569

--font-mono: 'Space Mono', monospace
--font-sans: 'DM Sans', system-ui, sans-serif
```

Fonts loaded via Google Fonts `@import`:
`https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=DM+Sans:wght@400;500;600&display=swap`

---

## Steps

### Phase 1 — CSS (blocks nothing else, do first)

1. Replace `app/static/css/app.css` entirely with:
   - `@import` Google Fonts URL at top
   - `:root` token block
   - Minimal reset (keep legacy utility aliases so existing template classes still resolve)
   - Keep all layout/spacing/typography utility classes (unchanged semantically, values now reference vars)
   - New component blocks:
     - `.start-screen`, `.start-card`, `.start-logo`, `.start-tagline`
     - `.how-to-play`, `.how-to-play-title`, `.how-to-play-list`, `.step-num`
     - `.btn-start`
     - `.game-screen`, `.game-header`, `.game-header-logo`, `.btn-back`
     - `.game-hint`, `.bingo-banner`, `.board-wrapper`
     - `.bingo-grid`, `.board-cell`, `.board-cell.is-marked`, `.board-cell.is-winning`, `.board-cell.is-free`, `.cell-check`
     - `.modal-overlay`, `.modal-card`, `.modal-icon`, `.modal-title`, `.modal-subtitle`, `.btn-keep-playing`
   - Two keyframe animations: `modal-in` (scale+translate spring), `icon-pop`

### Phase 2 — Templates (parallel, all depend on Phase 1 classes)

2. **`base.html`**: update `theme-color` meta to `#0a0f1a`.

3. **`start_screen.html`**: rewrite using `.start-screen` wrapper, `.start-card`, `.start-logo` with `<span>` accent on "Ops", `.start-tagline`, `.how-to-play` card with numbered `.step-num` list items, `.btn-start` button. Keep all HTMX attributes identical (`hx-post="/start"`, `hx-target="#game-container"`, `hx-swap="outerHTML"`).

4. **`game_screen.html`**: rewrite using `.game-screen` wrapper, `.game-header` with `.btn-back` and `.game-header-logo`, `.game-hint`, `.bingo-banner` (conditional), `.board-wrapper`. Keep all HTMX attributes identical. Include partials unchanged.

5. **`bingo_board.html`**: rewrite using `.bingo-grid` container; each button uses `.board-cell` with conditional modifiers `.is-marked`, `.is-winning`, `.is-free`; `.cell-check` span. Keep all HTMX attributes identical (`hx-post="/toggle/{{ square.id }}"`, `hx-target="#game-container"`, `hx-swap="outerHTML"`).

6. **`bingo_modal.html`**: rewrite using `.modal-overlay`, `.modal-card`, `.modal-icon`, `.modal-title`, `.modal-subtitle`, `.btn-keep-playing`. Keep HTMX attributes identical.

### Phase 3 — Validation

7. Run `uv run ruff check .` — should be clean (no Python changes).
8. Run `uv run ruff format .` — no diff expected.
9. Run `uv run pytest` — all 25 tests should pass (logic/API unchanged).
10. Manual smoke test at `http://localhost:8000`:
    - Start screen renders with dark background and dot-grid
    - Board renders 5×5 with dark cells
    - Tapping a cell marks it green
    - Winning line highlights amber-yellow
    - Bingo modal appears with spring animation

---

## Decisions

- Dark theme only (no light/dark toggle) — keeps things sharp and distinctive.
- Space Mono board text: intentional ops/terminal feel; readable at small sizes thanks to monospace metrics.
- No JS — all animation via CSS keyframes.
- Board uses a `gap: 3px` grid with the gap itself styled as the border color (`.bingo-grid` background = border color), eliminating nested border logic.
- Legacy utility class aliases kept in CSS so any residual utility classes in templates still resolve without error.
- Google Fonts loaded via `@import` in CSS (not a `<link>` tag) — simpler, no `base.html` changes needed for fonts.
- Scope: CSS + 5 templates only. No Python changes. No new files.
