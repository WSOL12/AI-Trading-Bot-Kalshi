---

## What Is This?

**Ensemble Trader** is a bot that trades on [Kalshi](https://kalshi.com) prediction markets. It uses five different AI models to decide what to buy and sell, manages risk, and can run in paper mode or with real money.

**This is experimental. For learning and research only.** You can lose money. Trade only what you can afford to lose. This is not financial advice. No warranty.

---

## How It Works

Data flows in from Kalshi’s API, WebSocket stream, and news feeds. Five AI models analyze it and vote on each decision. When they disagree too much, the bot trades smaller or skips the trade. Orders go to Kalshi through an order router, and results are tracked in a local database.

**AI ensemble:**
- Grok-4 (xAI) – Lead forecaster
- Claude Sonnet 4 (OpenRouter) – News analyst
- GPT-4o (OpenRouter) – Bull researcher
- Gemini 2.5 Flash (OpenRouter) – Bear researcher
- DeepSeek R1 (OpenRouter) – Risk manager

Votes are weighted. Debate and consensus drive final decisions, and confidence is calibrated.

**Strategies:**
- Directional (about 50%): AI edge + Kelly sizing
- Market making (about 40%): Limit orders for bid‑ask spread
- Arbitrage (about 10%): Cross‑market scanning

**Exits:** Trailing take‑profit, stop‑loss, confidence decay, time‑based (max 10 days), and volatility‑adjusted thresholds.

---

## Install & Run

**Requirements:** Python 3.12+, Kalshi API access, xAI key, OpenRouter key.

```bash
git clone <repository-url>
cd <project-folder>
python -m venv venv
source venv/bin/activate   # or venv\Scripts\activate on Windows
pip install -e ".[dev,dashboard]"
```

**Config:**
1. Copy `env.template` to `.env`
2. Set `KALSHI_API_KEY`, `XAI_API_KEY`, `OPENROUTER_API_KEY` (and optionally `OPENAI_API_KEY`)
3. Put your Kalshi private key file at project root as `kalshi_private_key` (no extension)

**Database setup:**
```bash
python -m src.utils.database
```

**Run:**
```bash
python cli.py run --paper          # Paper trading
python cli.py run --live           # Live
python cli.py dashboard            # Streamlit dashboard
python cli.py status               # Balance and positions
python cli.py health               # Config/connection check
```

Or: `python beast_mode_bot.py`, `beast_mode_bot.py --live`, `beast_mode_bot.py --dashboard`.

---

## Layout

- `beast_mode_bot.py` – Main entry
- `cli.py` – CLI for run, dashboard, status, health, backtest
- `src/agents/` – Ensemble agents
- `src/clients/` – Kalshi, xAI, OpenRouter, WebSocket
- `src/config/` – Settings
- `src/jobs/` – Ingest, decide, execute, track, evaluate
- `src/strategies/` – Market making, portfolio, etc.
- `scripts/` – Utilities
- `tests/` – Pytest suite

---

## 🧪 Paper Mode & Dashboard

Log what the bot would trade without real orders:

```bash
python paper_trader.py
python paper_trader.py --loop --interval 900
python paper_trader.py --settle
python paper_trader.py --dashboard
python paper_trader.py --stats
```

Dashboard output: `docs/paper_dashboard.html`.

---

## Tuning

Edits go in `src/config/settings.py`. Highlights:

- `max_position_size_pct`, `max_positions`, `kelly_fraction`
- `min_volume`, `max_time_to_expiry_days`, `min_confidence_to_trade`
- `max_daily_loss_pct`, `daily_ai_cost_limit`

Ensemble and debate settings: `EnsembleConfig` in the same file.

---

## Metrics

Everything is logged to `trading_system.db`: trades, AI decisions, costs. Use the dashboard and `scripts/` to inspect P&L, win rate, Sharpe, drawdown, AI confidence, and strategy breakdowns.

---

## Dev

```bash
python run_tests.py
# or: pytest tests/
black src/ tests/ cli.py beast_mode_bot.py
isort src/ tests/ cli.py beast_mode_bot.py
mypy src/
```

To add a strategy: implement it in `src/strategies/`, wire into `unified_trading_system.py`, update settings, add tests.

---

## Contributing

Fork, branch, commit, open a PR. Use Black and isort. Add tests for new features.

---

## License

MIT. See [LICENSE](LICENSE).
