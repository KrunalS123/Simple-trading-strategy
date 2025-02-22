# Automated Stock Moving Average Trading Strategy

Creating an automated trading system with Python involves developing a program capable of analyzing market data and making trading decisions. We'll utilize yfinance to fetch stock market data, Pandas and NumPy to organize and analyze it, and Matplotlib to generate charts for visualizing trends and patterns. The core idea is to leverage historical stock prices and perform basic calculations to inform buying or selling decisions. Before deploying the system for real trading, we will test it using historical data to evaluate its performance. This approach will enable us to build a simple yet intelligent trading system that operates autonomously.


## Code Overview
- Downloads historical stock data from Yahoo Finance
- Implements moving average crossover strategy
- Compares strategy performance vs buy-and-hold
- Generates interactive visualizations
## Step 1: Install & Import Required Libraries

* Install yfinance: Fetch stock market data.
* Install pandas and numpy: Perform data manipulation and numerical operations.
* Install matplotlib: Create visualizations.
```
pip install yfinance
pip installpandas
pip install numpy
pip installmatplotlib

import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt
```

## Step 3: Fetch Historical Stock Data

Define the stock ticker (e.g., AAPL for Apple).
Setting the start and end dates for our analysis.
Use yfinance to fetch the data.

# Stock and date settings

```
stock = "TSLA"  # Try changing this to other stocks like "AAPL" or "GOOG"
start = "2010-01-01"
end = "2025-01-01"

# Get stock data
data = yf.download(stock, start=start, end=end)

# Display first few rows
print(data.head()) 

```
## Step 4: Calculate Technical Indicators
*Indicators help identify trends and generate trading signals. Moving averages smooth out the price data over a set number of days, making it easier to identify trends and filter out short-term noise.

1. Simple Moving Averages (SMA):
 
* SMA50 (short-term trend).
* SMA200 (long-term trend).
* Use rolling().mean() to compute the averages over a specified window.
```
# Calculate moving averages
data['SMA50'] = data['Close'].rolling(50).mean()  # 50-day average
data['SMA200'] = data['Close'].rolling(200).mean() # 200-day average
```
##Step 5: Define Buy/Sell Signals

### Why signals are used:

* Helps in making trading decisions based on historical trends.
* A clear visual guide for when to enter or exit the market based on crossovers.
* Trading signals are created based on SMA crossovers:

* Buy Signal (1): When SMA50 > SMA200.
* Sell Signal (-1): When SMA50 < SMA200.

```
# Create trading signals
data['Signal'] = 0  # Start with no signal
data.loc[data['SMA50'] > data['SMA200'], 'Signal'] = 1   # Buy signal
data.loc[data['SMA50'] < data['SMA200'], 'Signal'] = -1  # Sell signal
```

## Step 6: Simulate Trades
Simulate the strategy by calculating daily and cumulative returns.
```
# Calculate returns
data['Position'] = data['Signal'].shift(1)  # Previous day's signal
data['Daily Return'] = data['Close'].pct_change()  # Daily price changes
data['Strategy Return'] = data['Position'] * data['Daily Return']

# Calculate growth of $1 investments
data['Market Growth'] = (1 + data['Daily Return']).cumprod()
data['Strategy Growth'] = (1 + data['Strategy Return']).cumprod()
```
### Explanation:

* Position: Reflects the previous day’s signal.
* Daily Return: The percentage change in stock price from the previous day.
 Strategy Return: Returns achieved using the trading strategy.
* Cumulative Returns: Tracks the growth of $1 invested.

## Step 7: Visualize Data
Visualize the stock price, SMA and returns to better understand the strategy.

```
# Plot price and moving averages
plt.figure(figsize=(12, 6))
plt.plot(data['Close'], label='Stock Price')
plt.plot(data['SMA50'], label='50-Day Average')
plt.plot(data['SMA200'], label='200-Day Average')
plt.title(f"{stock} Price and Moving Averages")
plt.legend()
plt.show()
```
Output:

![image](https://github.com/user-attachments/assets/af900bd4-f219-4f39-a9a2-8b275e4590ce)


```# Plot investment growth
plt.figure(figsize=(12, 6))
plt.plot(data['Market Growth'], label='Buy & Hold')
plt.plot(data['Strategy Growth'], label='Moving Average Strategy')
plt.title("Investment Growth Comparison")
plt.legend()
plt.show()
```

output: 
![image](https://github.com/user-attachments/assets/061b8e2e-ca9a-4291-8769-6140488f36c0)



### Step 8: Evaluate Performance
Compare the cumulative returns of the strategy vs. holding the market.

```
# Print final results
market_result = data['Market Growth'].iloc[-1] - 1
strategy_result = data['Strategy Growth'].iloc[-1] - 1

print(f"Buy & Hold Return: {market_result:.1%}")
print(f"Strategy Return: {strategy_result:.1%}")

```
## Output:

* Buy & Hold Return: 25256.2%
* Strategy Return: 131.6%

  
### Why this step is important:

* Measures the profitability of the trading strategy against a buy-and-hold approach.
* Helps evaluate how well the strategy performs on historical data and provides a sense of risk-adjusted returns.





