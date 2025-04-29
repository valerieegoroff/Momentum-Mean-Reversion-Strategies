# Momentum-Mean-Reversion-Strategies
Side-by-side comparison of momentum and mean reversion equity strategies using Python, pandas, and finance.

# Momentum vs. Mean Reversion: Backtest and Strategy Comparison
This project explores and compares two classic equity trading strategies using historical data from 2015 to 2025:

**Momentum Strategy**: Buys the top 3 performing stocks each month based on their 12-month return.
**Mean Reversion Strategy**: Buys the bottom 3 performing stocks each week based on their most recent 1-week loss.

The goal was to see how these two strategies perform over time when applied to a small group of well-known S&P 500 stocks.

## What I Did
- Pulled adjusted close data using `yfinance`
- Calculated daily and multi-period returns using `pandas`
- Built signal logic for each strategy
- Forward-filled and shifted signals to simulate realistic execution
- Plotted cumulative performance over time

## Tools Used
- Python  
- pandas, NumPy  
- matplotlib  
- yfinance

## Takeaways
Momentum held up well over the full backtest period. Mean reversion was more volatile and less consistent, especially during long bull runs. Neither strategy included transaction costs, which would be important to model in future versions.

---

This was a personal project to sharpen my backtesting and data analysis skills. 
