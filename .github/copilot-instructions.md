# Soc Ops — Project Guidelines

Social Bingo web game for in-person mixers. Players find people matching prompts on a 5×5 board to get 5 in a row.

## Development Checklist

Before completing any change, you MUST run and fix all issues in this order:

```bash
uv run ruff check .      # 1. Lint — fix all errors before proceeding
uv run ruff format .     # 2. Format — auto-apply, then confirm no diff
uv run pytest            # 3. Test — all tests must pass
```

## Tech Stack

- **Backend**: Python 3.13+, FastAPI, Jinja2, `itsdangerous` sessions; `uv` only (no pip)
- **Frontend**: HTMX (no JS frameworks); custom CSS utilities in `app/static/css/app.css`
- **Models**: Pydantic v2, `ConfigDict(frozen=True)`; mutate via `model_copy(update={...})`
- **Lint/format**: Ruff, target Python 3.13, line length 88

## Architecture

| Layer | File | Responsibility |
|---|---|---|
| Data | `app/data.py` | Static constants |
| Models | `app/models.py` | Immutable Pydantic models + `GameState` StrEnum |
| Logic | `app/game_logic.py` | Pure functions, no side-effects |
| Service | `app/game_service.py` | `GameSession` dataclass + in-memory `_sessions` dict |
| Routes | `app/main.py` | FastAPI routes wiring HTTP to service layer |

All state is in-memory, keyed by UUID stored in a signed cookie — no database.

## Templates, HTMX & CSS

- `components/` → partial HTML fragments returned by HTMX-targeted routes; write no custom JS
- HTMX pattern: `hx-post`, `hx-target`, `hx-swap="outerHTML"`
- CSS is hand-authored in `app/static/css/app.css` — design tokens via CSS variables in `:root`, plus utility classes and component classes
- See the **Design System** section below for current tokens, fonts, and component classes

## Design System

All styling lives in `app/static/css/app.css`. The design is a dark "ops room" aesthetic.

### Fonts

Loaded via `@import` from Google Fonts — no `<link>` tag needed in templates.

| Variable | Value | Usage |
|---|---|---|
| `--font-mono` | Space Mono | Logo, board cells, labels, buttons |
| `--font-sans` | DM Sans | Body copy, instructions |

### Color Tokens

| Variable | Value | Usage |
|---|---|---|
| `--bg` | `#0a0f1a` | App background |
| `--surface` | `#111827` | Cards, header |
| `--surface-2` | `#1a2235` | Hover states, free space cell |
| `--border` | `#1e2d45` | Default borders |
| `--border-hi` | `#2a3f5f` | Elevated borders |
| `--accent` | `#0ea5e9` | Primary action, logo highlight |
| `--accent-dim` | `#0284c7` | Pressed/active accent state |
| `--marked` | `#0d2b1e` | Marked cell background |
| `--marked-text` | `#4ade80` | Marked cell text |
| `--marked-check` | `#22c55e` | Checkmark on marked cells |
| `--win` | `#1a1a00` | Winning-line cell background |
| `--win-text` | `#fde047` | Winning-line cell text |
| `--text-primary` | `#f1f5f9` | Primary text |
| `--text-secondary` | `#94a3b8` | Secondary / subdued text |
| `--text-muted` | `#475569` | Hints, labels, placeholders |

### Component Classes

Prefer component classes over composing bare utility classes for new UI work.

| Class | Element |
|---|---|
| `.start-screen` | Full-page start screen wrapper (dot-grid + radial glow background) |
| `.start-card` | Centered content card on start screen |
| `.start-logo` | H1 logo — Space Mono, `span` child gets `--accent` color |
| `.start-tagline` | Uppercase mono tagline below logo |
| `.how-to-play` | Instruction card with dark surface border |
| `.how-to-play-title` | Uppercase accent label inside instruction card |
| `.how-to-play-list` | `<ul>` wrapper for step items |
| `.step-num` | Circular numbered badge inside each step |
| `.btn-start` | Primary CTA button on start screen |
| `.game-screen` | Full-page game wrapper |
| `.game-header` | Top bar with back button and logo |
| `.game-header-logo` | Logo in header — wrap accent word in `<span>` |
| `.btn-back` | Back/reset button in header |
| `.game-hint` | Subdued uppercase mono hint below header |
| `.bingo-banner` | Amber banner shown when `session.has_bingo` |
| `.board-wrapper` | Flex container centering the bingo grid |
| `.bingo-grid` | 5×5 CSS grid; gap background acts as cell borders |
| `.board-cell` | Individual square button; add `.is-marked`, `.is-winning`, `.is-free` |
| `.cell-check` | Checkmark badge inside a marked cell; add `.is-winning` for amber tint |
| `.modal-overlay` | Fixed fullscreen overlay with blur backdrop |
| `.modal-card` | Centered modal panel with spring-in animation |
| `.modal-icon` | Large emoji icon at top of modal |
| `.modal-title` | Large mono title in modal (amber for bingo win) |
| `.modal-subtitle` | Subdued body text in modal |
| `.btn-keep-playing` | Primary CTA inside modal |

### Rules

- Always use CSS variables (`var(--accent)`) rather than hardcoding hex values
- Add new utilities or component classes to `app/static/css/app.css` — never inline `style` attributes
- Utility classes are available as composable building blocks but component classes are preferred for named UI sections
- No JavaScript — animations are CSS-only (`@keyframes modal-in`, `@keyframes icon-pop`)

## Testing

- `tests/test_api.py` — integration via `TestClient(app)`; check status + body text
- `tests/test_game_logic.py` — unit tests on pure functions, no mocking
- Group tests in classes by concern (e.g., `class TestGenerateBoard:`)
