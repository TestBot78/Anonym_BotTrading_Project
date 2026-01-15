# Anonym_Candidature
Quantitative Trading Pipeline

A sophisticated algorithmic trading system implementing multi-factor signal processing with dynamic leverage optimization and liquidity-aware position sizing, designed for automated portfolio management across multiple brokers.

> **Project Focus**: This project was built to understand how quantitative signals behave once realistic execution, liquidity and risk constraints are applied, with a strong focus on risk management rather than pure alpha maximization.

## Project Overview

This project implements a production-ready quantitative trading pipeline that combines:

- **Multi-timeframe signal processing** (daily + hourly data fusion)
- **Dynamic leverage optimization** (1.0x - 3.0x based on signal confidence)
- **Real benchmark data** (SPY returns for accurate beta/momentum calculations)
- **Liquidity constraints** (10% ADV cap prevents market impact)
- **Risk management framework** (drawdown limits, position sizing, tail hedging)
- **Dual-broker architecture** (Alpaca for data, IBKR for execution)

### Key Features

| Feature | Description |
|---------|-------------|
| **Hybrid Signal System** | 70% daily signals + 30% hourly timing signals |
| **5 Alpha Factors** | Volatility, Momentum, Tail Risk, Beta, Carry |
| **Real Benchmark** | Uses actual SPY returns instead of approximations |
| **Liquidity Constraints** | Maximum 10% of 20-day ADV per position |
| **Dynamic Weighting** | Correlation-adjusted weight optimization |
| **Stress Testing** | 6 historical crisis scenarios (1987, 1998, 2000, 2008, 2010, 2020) |
| **Real-time Execution** | Paper trading with live market data |

## Architecture

```
trading_pipeline/
│
├── Core Engine
│   ├── paper_trading.py          # Main trading engine (1,700 lines)
│   ├── enhanced_pipeline.py      # Signal processing & backtesting (1,690 lines)
│   └── hybrid_pipeline_weighted.py # Multi-timeframe fusion (200 lines)
│
├── Broker Adapters
│   ├── dual_broker_adapter.py    # Unified broker interface
│   └── ibkr_adapter.py           # Interactive Brokers integration
│
├── Configuration
│   └── trading_config.json       # Runtime parameters
│
└── Data Storage
    ├── paper_trading.db          # SQLite database
    ├── paper_trading_ibkr.db     # IBKR-specific database
    └── *.log                     # Execution logs
```

## Signal Processing

### Alpha Factors

The system implements 5 quantitative signals:

1. **Volatility Signal** - Mean-reversion on realized volatility z-scores
2. **Momentum Signal** - Excess returns vs SPY benchmark with fast/slow crossover
3. **Tail Risk Signal** - Expected Shortfall (CVaR) with asymmetry adjustment
4. **Beta Signal** - Dynamic beta exposure vs real market returns
5. **Carry Signal** - Volatility Risk Premium + mean reversion + seasonality

### Real Benchmark Integration

The system fetches actual SPY returns for accurate signal calculation:

```python
# Real SPY benchmark (not approximation)
spy_bars = api.get_bars('SPY', TimeFrame.Day, limit=500)
spy_returns = spy_bars['close'].pct_change()
```

This ensures:
- Accurate momentum excess returns
- Proper beta calculations
- Realistic signal correlation

### Hybrid Composition

Final signal = 0.70 × Daily_Composite + 0.30 × Hourly_Composite

## Risk Management

### Position Sizing with Liquidity Constraints

Positions are capped by both portfolio limits AND liquidity:

```python
# Maximum shares calculation
max_shares_portfolio = equity * max_position_size * max_leverage / price
max_shares_liquidity = ADV_20day * 0.10  # 10% of average daily volume

# Apply both constraints
final_shares = min(max_shares_portfolio, max_shares_liquidity)
```

### Position Limits

| Parameter | Value |
|-----------|-------|
| Maximum Position Size | 20% of portfolio per asset |
| Maximum Gross Leverage | 3.0x equity |
| Maximum Net Leverage | 1.5x equity |
| **Liquidity Cap** | **10% of 20-day ADV** |

### Drawdown Controls

| Threshold | Action |
|-----------|--------|
| -5% daily | Warning, reduce new positions |
| -10% daily | Halt new positions |
| -15% total | Flatten 50% of portfolio |
| -20% total | Full liquidation |

### Tail Hedging

Stateful tail hedge activates when drawdown < -5%:
- Convex payoff: `hedge_size × downside^1.5 × 3`
- Daily cost: ~2 bps when active
- Deactivation: drawdown recovers > -2.5%

## Installation

### Prerequisites

- Python 3.10+
- Interactive Brokers TWS/Gateway (optional)

### Setup

```bash
# Clone repository
git clone https://github.com/yourusername/trading_pipeline.git
cd trading_pipeline

# Install dependencies
pip install alpaca-trade-api pandas numpy scipy ib_insync requests pytz
```

### Configuration

Edit `trading_config.json`:

```json
{
  "alpaca_api_key": "YOUR_KEY",
  "alpaca_api_secret": "YOUR_SECRET",
  "alpaca_base_url": "https://paper-api.alpaca.markets",
  "symbols": ["AAPL", "MSFT", "GOOGL", "AMZN", "NVDA"],
  "max_leverage": 3.0,
  "max_position_size": 0.2
}
```

## Usage

### Initialize

```bash
python paper_trading.py init
```

### Run Paper Trading

```bash
python paper_trading.py run trading_config.json
```

### Run Backtest Only

```bash
python enhanced_pipeline.py
```

## Performance Metrics

The system tracks:

- **Sharpe Ratio** (risk-adjusted returns)
- **Maximum Drawdown** (peak-to-trough decline)
- **Information Coefficient** (signal predictive power)
- **Hit Rate** (% profitable signals)
- **Turnover-Adjusted IC** (after transaction costs)

## Stress Testing

6 historical stress scenarios:

| Scenario | Crash | Duration | Vol Multiplier |
|----------|-------|----------|----------------|
| Black Monday 1987 | -20% | 1 day | 5× |
| LTCM 1998 | -15% | 5 days | 3× |
| Dot-com 2000 | -30% | 20 days | 2.5× |
| Financial Crisis 2008 | -40% | 60 days | 4× |
| Flash Crash 2010 | -10% | 1 day | 10× |
| COVID 2020 | -35% | 15 days | 6× |

## Technical Specifications

### Data Sources

- **Alpaca**: 10 years daily bars, 2 years hourly bars, real SPY benchmark
- **IBKR**: Real-time execution, no PDT restrictions

### Code Metrics

| File | Lines | Description |
|------|-------|-------------|
| `paper_trading.py` | 1,700 | Main engine with order execution |
| `enhanced_pipeline.py` | 1,690 | Signal computation, backtesting |
| `hybrid_pipeline_weighted.py` | 200 | Multi-timeframe signal fusion |
| `dual_broker_adapter.py` | 175 | Alpaca data + IBKR execution |
| `ibkr_adapter.py` | 418 | IBKR API wrapper |
| **Total** | **~4,200** | Production-ready system |

## Author

**Gonzi** - ESSCA School of Management  
MSc Finance & Data Analytics Candidate

## License

Educational and personal use only. Not financial advice.

---

*Last updated: January 2026*
