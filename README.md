# 🤖 Agentic AI Assistant

A modular, multi-agent personal assistant that answers natural-language
requests about **weather**, **stock prices**, and **jokes**, can **create
calendar events**, and can **email results** — with a Streamlit dashboard
and a shared JSON context tracker for orchestration.

![CI](https://github.com/Gayathri-Reddy874/agentic-ai-assistant/actions/workflows/ci.yml/badge.svg)
![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![License](https://img.shields.io/badge/license-MIT-green)

## Features

| Agent | Capability |
|---|---|
| 🌦️ `weather_agent` | Real-time conditions via OpenWeatherMap |
| 💹 `stock_agent` | Latest OHLC price data via `yfinance` |
| 😂 `joke_agent` | Random programming jokes |
| 📅 `calendar_agent` | Creates an event from a natural-language request |
| 📧 `notifier_agent` | Emails a summary via Gmail SMTP |

A lightweight shared **context tracker** (`core/context_manager.py`) records
each agent's status and result to a local JSON file, so a run's state can be
inspected or resumed independently of any single UI.

## Architecture

```
agentic-ai-assistant/
├── agents/                # One file per agent, each with a single public function
│   ├── weather_agent.py
│   ├── stock_agent.py
│   ├── joke_agent.py
│   ├── calendar_agent.py
│   └── notifier_agent.py
├── core/
│   └── context_manager.py # Shared JSON context read/write helpers
├── tests/
│   └── test_agents.py
├── main.py                # CLI orchestrator: runs all agents, emails a summary
├── streamlit_app.py        # Interactive dashboard: routes free-text to an agent
├── requirements.txt
├── requirements-dev.txt
└── .env.example
```

Each agent is a plain function with no shared mutable state, so agents can be
tested, imported, or swapped independently. Routing (in `streamlit_app.py`)
and orchestration (in `main.py`) are kept separate from agent logic.

## Getting started

### 1. Clone and install

```bash
git clone https://github.com/Gayathri-Reddy874/agentic-ai-assistant.git
cd agentic-ai-assistant
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### 2. Configure environment variables

```bash
cp .env.example .env
```

Fill in `.env`:

| Variable | Required for | Notes |
|---|---|---|
| `OPENWEATHER_API_KEY` | Weather agent | [Get a free key](https://openweathermap.org/api) |
| `EMAIL_SENDER` | Email notifications | Gmail address |
| `EMAIL_PASSWORD` | Email notifications | A Gmail **App Password**, not your login password |
| `NOTIFY_EMAIL` | CLI (`main.py`) | Recipient for the automatic daily summary |
| `DEFAULT_STOCK_TICKER` | CLI (`main.py`) | Defaults to `AAPL` |

> ⚠️ **Never commit `.env`, `credentials.json`, or `token.pickle`.** They're
> already excluded via `.gitignore`. If any secret has previously been
> committed or shared, rotate it immediately.

### 3. Run

**CLI orchestrator** (runs weather + stock + joke, emails a summary if `NOTIFY_EMAIL` is set):

```bash
python main.py
```

**Streamlit dashboard**:

```bash
streamlit run streamlit_app.py
```

Then try prompts like:
- "What's the weather in Hyderabad?"
- "Get AAPL stock"
- "Tell me a joke"
- "Book a meeting on 6th April 9AM"

## Testing

```bash
pip install -r requirements-dev.txt
pytest -v
ruff check .
```

CI runs lint + tests on every push via GitHub Actions (`.github/workflows/ci.yml`).

## Roadmap

- [ ] Wire `calendar_agent` to the real Google Calendar API (OAuth)
- [ ] Add response caching for repeated weather/stock lookups
- [ ] Replace keyword-based routing with an LLM-based intent classifier
- [ ] Add structured logging / observability hooks

## License

MIT — see [LICENSE](LICENSE).
