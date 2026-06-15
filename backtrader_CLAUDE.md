# Claude Project Context: backtrader

This file provides codebase context for the **backtrader** repository, used as part of an AI trading-knowledge project.

## Project Overview
* **Name**: backtrader
* **Language**: Python (2.7 / 3.x — project is no longer actively maintained, but widely used and stable)
* **Type**: Event-driven backtesting and live-trading library
* **Purpose**: Backtest trading strategies on historical data and (via broker integrations) execute them live, with built-in charting/plotting.
* **License**: GPL-3.0
* **Status**: Archived/unmaintained — treat as a stable reference implementation rather than expecting upstream updates or new feature support.

## Core System Boundaries
* **Cerebro engine**: the central orchestrator (`bt.Cerebro`) that wires together data feeds, strategies, brokers, sizers, analyzers, and observers, then runs the simulation.
* **Lines architecture**: data and indicators are represented as "Lines" (time series arrays) accessed via indexing (`self.data.close[0]` = current bar, `[-1]` = previous bar).
* **Broker simulation**: built-in `BackBroker` simulates cash, commissions, margin, and order execution for backtests; live brokers (Interactive Brokers, Oanda, etc.) available via separate store modules.

## Repository Structure & Key Directories
* `/backtrader/` - Core package
  * `/backtrader/feeds/` - Data feed classes (CSV, Pandas, Yahoo, live feeds)
  * `/backtrader/indicators/` - Built-in indicators (SMA, RSI, MACD, Bollinger Bands, etc.)
  * `/backtrader/analyzers/` - Performance analyzers (Sharpe ratio, drawdown, returns)
  * `/backtrader/brokers/` - Broker simulation and live broker integrations
  * `/backtrader/sizers/` - Position sizing logic
  * `/backtrader/strategy.py` - `Strategy` base class
  * `/backtrader/cerebro.py` - `Cerebro` engine
* `/samples/` - Example scripts demonstrating strategies, data feeds, and plotting
* `/docs/` - Documentation (quickstart, indicators reference, broker docs)

## Standard Operational Commands
* **Install**: `pip install backtrader` (add `pip install matplotlib` separately for plotting on newer versions)

## Standard Python API Usage (Strategy Blueprint)
```python
import backtrader as bt

class MyStrategy(bt.Strategy):
    params = (("period", 20),)

    def __init__(self):
        self.sma = bt.indicators.SimpleMovingAverage(self.data.close, period=self.params.period)

    def next(self):
        if self.data.close[0] > self.sma[0] and not self.position:
            self.buy()
        elif self.data.close[0] < self.sma[0] and self.position:
            self.sell()

cerebro = bt.Cerebro()
cerebro.addstrategy(MyStrategy)
cerebro.adddata(data_feed)
cerebro.broker.setcash(10000)
cerebro.run()
cerebro.plot()
```

## AI Assistant Guidelines for this Project
1. **Cerebro-centric**: every backtest example should set up a `Cerebro` instance, add data feed(s), strategy, and (optionally) sizers/analyzers before calling `.run()`.
2. **Lines indexing**: explain `[0]` = current bar, `[-1]` = previous bar — a frequent source of confusion for newcomers.
3. **Maintenance status**: since the project is unmaintained, flag known issues with newer pandas/matplotlib versions and suggest pinning compatible versions (e.g. older matplotlib) if plotting breaks.
4. **Migration note**: if a user wants active development/live multi-asset support, mention `nautilus_trader` or `freqtrade` as more actively maintained alternatives — but backtrader remains excellent for learning backtesting concepts.
5. **Indicators**: prefer backtrader's built-in `bt.indicators.*` over reimplementing common indicators manually.
