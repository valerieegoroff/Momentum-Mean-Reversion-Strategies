import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf
import statsmodels.api as sm

# Selecting 10 diversified S&P 500 tickers
tickers = ['AAPL', 'MSFT', 'GOOGL', 'AMZN', 'META', 'NVDA', 'JPM', 'UNH', 'XOM', 'PG']

# Downloading adjusted historical prices
# auto_adjust=True makes 'Close' equal to 'Adj Close' by adjusting for splits/dividends
data = yf.download(tickers, start='2015-01-01', end='2025-01-01', auto_adjust=True)['Close']

# Preview the data
data.head()

# Calculating simple daily percentage returns
returns = data.pct_change().dropna()
returns.head()

# Calculating trailing 12-month returns (~252 trading days)
momentum_scores = data.pct_change(252)

# Resampling to monthly frequency for rebalancing
momentum_monthly = momentum_scores.resample('M').last()

# Generating signals: long top 3 stocks each month based on 1-year return
def generate_momentum_signals(momentum_df, top_n=3):
    signals = pd.DataFrame(index=momentum_df.index, columns=momentum_df.columns)
    for date in momentum_df.index:
        top_stocks = momentum_df.loc[date].nlargest(top_n).index
        signals.loc[date, top_stocks] = 1
    return signals.reindex_like(momentum_df).fillna(0)

momentum_signals = generate_momentum_signals(momentum_monthly)

# Resampling adjusted prices to weekly frequency (every Friday)
weekly_prices = data.resample('W-FRI').last()

# Calculating 1-week returns from weekly prices
weekly_returns = weekly_prices.pct_change()

# Generating signals: long bottom 3 stocks each week based on worst 1-week return
def generate_mean_reversion_signals(returns_df, bottom_n=3):
    signals = pd.DataFrame(index=returns_df.index, columns=returns_df.columns)
    for date in returns_df.index:
        bottom_stocks = returns_df.loc[date].nsmallest(bottom_n).index
        signals.loc[date, bottom_stocks] = 1
    return signals.reindex_like(returns_df).fillna(0)

mean_reversion_signals = generate_mean_reversion_signals(weekly_returns)

# Expanding signals to daily frequency so they align with daily returns
momentum_signals_filled = momentum_signals.reindex(returns.index).ffill()
mean_reversion_signals_filled = mean_reversion_signals.reindex(returns.index).ffill()

# Shifting signals by 1 day to avoid look-ahead bias
momentum_positions = momentum_signals_filled.shift(1)
mean_reversion_positions = mean_reversion_signals_filled.shift(1)

# Calculating strategy returns by averaging returns of selected stocks
momentum_portfolio_returns = (momentum_positions * returns).mean(axis=1)
mean_reversion_portfolio_returns = (mean_reversion_positions * returns).mean(axis=1)

# Dropping initial NaNs
momentum_portfolio_returns = momentum_portfolio_returns.dropna()
mean_reversion_portfolio_returns = mean_reversion_portfolio_returns.dropna()

# Preview
momentum_portfolio_returns.head(), mean_reversion_portfolio_returns.head()

# Calculating cumulative portfolio value
momentum_cumulative = (1 + momentum_portfolio_returns).cumprod()
mean_reversion_cumulative = (1 + mean_reversion_portfolio_returns).cumprod()

# Plotting both cumulative returns on the same chart
plt.figure(figsize=(12,6))
plt.plot(momentum_cumulative, label='Momentum Strategy', linewidth=2)
plt.plot(mean_reversion_cumulative, label='Mean Reversion Strategy', linewidth=2)
plt.title('Cumulative Returns: Momentum vs. Mean Reversion', fontsize=14)
plt.xlabel('Date')
plt.ylabel('Portfolio Value')
plt.legend()
plt.grid(True)
plt.show()
