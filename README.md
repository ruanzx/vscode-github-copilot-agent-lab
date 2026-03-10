# Soc Ops

Soc Ops is a social bingo game for in-person mixers, workshops, and team events.
Players get a 5x5 board of prompts, then move around the room finding people who
match each square. First to 5 in a row wins.

**Play now:** https://madebygps.github.io/vscode-github-copilot-agent-lab/

**Lab guide:** https://madebygps.github.io/vscode-github-copilot-agent-lab/docs/

## Why This Project

- Makes networking less awkward by turning intros into a game.
- Works well for hackathons, onboarding sessions, meetups, and classes.
- Built as a practical lab for modern AI-assisted development workflows.

## How It Works

1. Start a new game to generate a randomized bingo board.
2. Talk to people and mark squares as you find matching prompts.
3. Keep going until you complete 5 in a row.
4. Reset and play again with a fresh board.

## Highlights

- FastAPI backend with server-rendered Jinja templates
- HTMX interactions (no frontend framework)
- In-memory, cookie-keyed session state
- Clear separation between data, logic, service, and routes
- Test coverage for both API behavior and game logic

## Quickstart

### Prerequisites

- Python 3.13+
- `uv`

### Install

```bash
uv sync
```

### Run Locally

```bash
uv run uvicorn app.main:app --reload --port 8000
```

Open `http://localhost:8000`.

## Developer Workflow

Run checks in this order:

```bash
uv run ruff check .
uv run ruff format .
uv run pytest
```

## Project Structure

| Layer | File | Responsibility |
|---|---|---|
| Data | `app/data.py` | Static prompts and constants |
| Models | `app/models.py` | Immutable Pydantic models + game state enum |
| Logic | `app/game_logic.py` | Pure game rules and board calculations |
| Service | `app/game_service.py` | Session orchestration in memory |
| Routes | `app/main.py` | FastAPI endpoints and template responses |

## Lab Guide

Follow the full build and customization walkthrough:

| Part | Topic |
|---|---|
| [00](https://madebygps.github.io/vscode-github-copilot-agent-lab/docs/step.html?step=00-overview) | Overview and checklist |
| [01](https://madebygps.github.io/vscode-github-copilot-agent-lab/docs/step.html?step=01-setup) | Setup and context engineering |
| [02](https://madebygps.github.io/vscode-github-copilot-agent-lab/docs/step.html?step=02-design) | Design-first frontend |
| [03](https://madebygps.github.io/vscode-github-copilot-agent-lab/docs/step.html?step=03-quiz-master) | Custom Quiz Master agent |
| [04](https://madebygps.github.io/vscode-github-copilot-agent-lab/docs/step.html?step=04-multi-agent) | Multi-agent development |

Offline lab files are available in [`.lab/`](.lab/).

## Deployment

The docs and playable demo are published to GitHub Pages from `main`.
