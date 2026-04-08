# apple-monte-carlo
# Apple Supply Chain Shock Simulator 🍎📉

A Monte Carlo simulation that models how manufacturing disruptions affect Apple's (AAPL) stock price — calibrated from **real historical events** and enriched with **live news sentiment analysis**.

> Built as a beginner data science project exploring quantitative finance concepts using Python.

---

## What This Project Does

Most stock simulators use arbitrary shock values. This one doesn't.

Instead it:
- Pulls **real AAPL price history** via `yfinance` to compute actual volatility and drift
- Measures **what Apple's stock actually did** during 5 real supply chain / geopolitical shocks
- Uses those real numbers to **calibrate the simulation shock** (no guessing)
- Fetches **live Apple headlines** from Google News and scores their sentiment
- Combines both signals to run **1,000 Monte Carlo paths** via Geometric Brownian Motion
- Outputs a **5-panel dashboard** with paths, distributions, historical context and sentiment

---

## The Core Idea

The simulation is grounded in a real-world question:

> *If Apple shuts down a cluster of factories representing X% of its total production, how much should the stock move?*

The key insight the project demonstrates: **scale matters enormously**. A factory representing 2% of Apple's global production barely registers. One representing 25% is catastrophic. The sensitivity analysis panel visualises exactly this relationship.

---

## Dashboard Output

The simulation produces a 5-panel chart:

| Panel | What it shows |
|-------|--------------|
| Monte Carlo paths | 1,000 simulated price trajectories over 1 year with P10/P50/P90 bands |
| Historical shocks | Real % drops Apple suffered during past events (COVID, tariffs, etc.) |
| Final distribution | Histogram of where prices land after 1 year, coloured by bear/neutral/bull |
| Headline sentiment | Live news headlines scored from -1 (bearish) to +1 (bullish) |
| Summary stats | Median, bull/bear cases, probability of gain, shock and sentiment used |

---

## Real Historical Events Used for Calibration

| Event | Category | What happened |
|-------|----------|--------------|
| COVID Crash (Feb–Mar 2020) | Supply chain | Global pandemic froze factories and stores |
| US-China Tariffs (May 2019) | Geopolitical | 25% tariffs on Chinese goods hit Apple hard |
| iPhone Demand Warning (Jan 2019) | Demand | Apple issued rare revenue warning on China weakness |
| China Factory Shutdown (Feb 2022) | Supply chain | Zhengzhou Foxconn lockdowns disrupted iPhone 14 production |
| Tariff Shock (Apr 2025) | Geopolitical | Sweeping tariffs announced; Apple fell on China exposure fears |

---

## How the Shock Is Calculated

```
Lost production  = factory_share × total iPhones
Net loss         = lost production × (1 - replacement_cap)   ← other factories absorb some
Revenue impact   = net loss × 0.50                           ← iPhones are ~50% of Apple revenue
Fundamental hit  = revenue impact × 1.5                     ← P/E sensitivity multiplier
Sentiment mod    = composite_sentiment × 0.05               ← from live headlines
Final shock      = fundamental hit + sentiment modifier + china_exit_bonus
```

---

## Quickstart

### 1. Clone the repo
```bash
git clone https://github.com/YOUR_USERNAME/apple-monte-carlo.git
cd apple-monte-carlo
```

### 2. Install dependencies
```bash
pip install yfinance numpy pandas matplotlib scipy textblob beautifulsoup4 requests
```

### 3. Run
```bash
python apple_mc_extended.py
```

Or open `apple_mc_extended.ipynb` in **Google Colab** — no installation needed.

---

## Configuration

All assumptions live in the `CONFIG` dict at the top of the file — easy to tweak:

```python
CONFIG = {
    "n_simulations":    1000,    # increase to 5000 for smoother distribution
    "factory_share":    0.08,    # fraction of total Apple production affected
    "replacement_cap":  0.60,    # how much other factories can absorb
    "sentiment_factor": 0.30,    # investor panic multiplier
    "china_exit_bonus": 0.02,    # positive signal from reducing China dependency
}
```

---

## Tech Stack

| Library | Purpose |
|---------|---------|
| `yfinance` | Download real AAPL stock price history |
| `numpy` | Geometric Brownian Motion simulation |
| `pandas` | Data manipulation |
| `matplotlib` | 5-panel dashboard visualisation |
| `textblob` | NLP sentiment scoring on headlines |
| `beautifulsoup4` + `requests` | Scrape Google News RSS for live headlines |

---

## Key Concepts

**Geometric Brownian Motion (GBM)** — the standard mathematical model for stock prices. Each day's return is drawn randomly from a normal distribution, scaled by the stock's historical volatility.

**Monte Carlo simulation** — instead of predicting one future, we simulate thousands of possible futures and look at the distribution of outcomes.

**Sentiment analysis** — TextBlob scores each headline's polarity from -1 to +1. The composite score across all headlines nudges the simulation slightly more bearish or bullish.

**Historical calibration** — rather than assuming a shock size, we measure what Apple actually dropped during comparable past events and use the median as our anchor.

---

## Project Structure

```
apple-monte-carlo/
├── apple_mc_extended.py     # main simulation script
├── apple_mc_extended.ipynb  # Colab-friendly notebook version (optional)
├── README.md
└── results/
    └── apple_mc_extended_results.png   # sample output chart
```

---

## Limitations & Assumptions

- Sentiment analysis uses TextBlob which is a simple rule-based model — not a fine-tuned financial NLP model
- Historical shocks are measured over varying time windows, not normalised to a fixed period
- The model assumes log-normal returns (standard GBM) which underestimates fat-tail events
- `china_exit_bonus` is a subjective assumption based on analyst commentary

---

## Ideas for Extension

- [ ] Add more companies (Samsung, Nvidia, TSMC) to compare shock sensitivity
- [ ] Replace TextBlob with a finance-specific model (FinBERT)
- [ ] Add jump-diffusion model to better capture crash events
- [ ] Build an interactive Streamlit dashboard
- [ ] Backtest: simulate past shocks and compare to what actually happened

---

## License

MIT — free to use, modify and distribute.
