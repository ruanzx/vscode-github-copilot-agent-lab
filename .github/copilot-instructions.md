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
- CSS utilities are hand-defined in `app/static/css/app.css` (Tailwind-like but plain CSS)
- Tokens: `.bg-accent` (`#2563eb`), `.bg-marked` (`#dcfce7`). See `.github/instructions/css-utilities.instructions.md`

## Testing

- `tests/test_api.py` — integration via `TestClient(app)`; check status + body text
- `tests/test_game_logic.py` — unit tests on pure functions, no mocking
- Group tests in classes by concern (e.g., `class TestGenerateBoard:`)
