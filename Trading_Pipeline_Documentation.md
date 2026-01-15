QUANTITATIVE TRADING PIPELINE

Multi-Factor Algorithmic Trading System

with Dynamic Leverage & Liquidity Constraints

Technical Documentation & Strategy Overview

Author: Gonzi

ESSCA School of Management

MSc Finance & Data Analytics

January 2026

Table of Contents

1.  Executive Summary

This document presents a comprehensive quantitative trading system
designed for systematic equity portfolio management. The system combines
multiple alpha factors with dynamic risk management and liquidity-aware
position sizing to generate risk-adjusted returns across varying market
conditions.

The pipeline implements a hybrid signal approach, combining 70% daily
strategic signals with 30% hourly tactical timing signals. Five distinct
alpha factors (Volatility, Momentum, Tail Risk, Beta, and Carry) are
weighted dynamically using correlation-aware optimization, with real SPY
benchmark data for accurate signal calculation.

Key Features

2.  System Architecture

The trading pipeline is built on a modular architecture separating
concerns between data acquisition, signal processing, risk management,
and order execution.

Component Overview

Data Flow

1.  Market Data: Alpaca API provides 10 years of daily bars, 2 years of
    hourly bars, plus real SPY returns for benchmark calculations.

2.  Signal Generation: The enhanced pipeline computes 5 alpha factors
    using real benchmark data and optimizes weights via
    correlation-aware objective.

3.  Position Sizing: Target positions are calculated with both portfolio
    constraints AND liquidity constraints (10% of 20-day ADV).

4.  Order Execution: IBKR handles actual trade execution, bypassing PDT
    restrictions available on Alpaca.

5.  Signal Processing

Real Benchmark Integration

A key improvement in this system is the use of actual SPY returns
instead of approximations. The system fetches real SPY data and aligns
it with individual stock returns for accurate momentum and beta
calculations.

Alpha Factors

Volatility Signal

Mean-reversion strategy on realized volatility z-scores. When volatility
spikes above historical norms, the signal goes short, expecting mean
reversion. The signal is amplified by volatility-of-volatility to
increase conviction during regime changes.

Momentum Signal (with Real Benchmark)

Combines absolute momentum (20-day vs 60-day returns) with relative
strength versus the real SPY benchmark. The 70/30 weighting favors
excess returns over simple price momentum. Using actual market returns
ensures accurate relative performance measurement.

Tail Risk Signal

Based on Expected Shortfall (CVaR at 5th percentile) adjusted by tail
asymmetry. Positions against extreme downside risk while accounting for
the shape of the return distribution.

Beta Signal (with Real Benchmark)

Rolling 60-day beta calculated against real SPY returns, multiplied by
beta volatility. Goes short when market sensitivity is high and
unstable, reducing systematic risk exposure during uncertain periods.

Carry Signal

Composite of Volatility Risk Premium proxy (50%), mean reversion to
252-day moving average (30%), and annual seasonality (20%). Captures
multiple sources of expected return.

Risk Budget Allocation

4.  Risk Management

Liquidity-Aware Position Sizing

A critical enhancement is the implementation of liquidity constraints.
Each position is capped at 10% of the 20-day Average Daily Volume (ADV),
preventing market impact and ensuring executable trades.

Position Limits

Drawdown Controls

Tail Hedging Mechanism

The system implements a stateful tail hedge that activates during market
stress. When portfolio drawdown exceeds -5%, the hedge provides convex
protection with a payoff proportional to (downside)\^1.5. This provides
increasing protection during severe market declines while limiting cost
during normal conditions.

5.  Stress Testing

The pipeline includes comprehensive stress testing against 6 historical
crisis scenarios. Each scenario injects realistic crash dynamics
including magnitude, duration, and volatility multipliers.

6.  Technical Specifications

Code Metrics

7.  Conclusion

This quantitative trading pipeline represents a systematic approach to
equity portfolio management, combining academic finance theory with
practical implementation considerations.

Key achievements of this project include:

1.  Implementation of 5 distinct alpha factors with real SPY benchmark
    integration

2.  Liquidity-aware position sizing (10% ADV constraint)

3.  Multi-timeframe signal fusion combining daily and hourly data

4.  Comprehensive risk management with drawdown controls and tail
    hedging

5.  Dual-broker architecture separating data quality from execution
    efficiency

6.  Integrated stress testing against 6 historical crisis scenarios

The system demonstrates proficiency in quantitative finance concepts
including factor investing, portfolio optimization, liquidity
management, risk management, and systematic trading execution.

Disclaimer: This is an educational project. Past performance does not
guarantee future results. Trading involves risk of loss.
