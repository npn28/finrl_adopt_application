# Automated Equity Trading using Deep Reinforcement Learning

## Overview

This project was inspired by a paper on automated trading using AI, ["Deep Reinforcement Learning for Automated Stock Trading: An Ensemble Strategy"](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3690996), code of which can be found here on [Github](https://github.com/AI4Finance-LLC/FinRL). The model adopted an ensemble approach using three actor-critic algorithms: Proximal Policy Optimization (PPO), Advantage Actor Critic (A2C), and Deep Deterministic Policy Gradient (DDPG) with an incorporation of turbulence index to address the analyst's risk aversion during market turbulence.

## Our tweaks to the model

We were interested in incorporating this model into live trading environment to test how well it performs in real-life setting, hence the start of this project. We did, however, made some changes in the library and in the model to adapt to our personal investment outlook:

### Training 
- Used S&P 500 as our stock universe instead of Dow Jones for a more diverse set of equities
- Added more technical indicators to the state space, including Bollinger Bands (upper & lower), SMA 30, and SMA 60 to existing list of MACD, RSI, CCI, and DX
- Our initial back test showed sharp drawdowns during market turbulences, much more volatile than the paper's model. Hence, we pushed the trading start time from 2009 back to 2000. Our model was able to learn from more market cycles, resulting in improved Sharpe Ratio and tempered volatility during market crash periods. The trained agents will then be exported to predict trade decision from daily data update.
- Training data comprises of 126 days, meaning it starts 126 days from current's date to current's date. 63 days (equivalent of 1 quarter) for validation window plus 63 days for rebalancing window. 

### Trading
- Data will be periodically refreshed and fed into model to generate trading decisions.
- Initial turbulence threshold is set based on data from 2000/01/01 to 06/23/2021 and is adjusted dynamically as the model enters trading period.
- Trading phases will load trained models (like mentioned above) to make decision based on past learning behavior from new input trade data and saved updated model again for next prediction time, aggragating its trading experiences further.

## Live Trading Environment

Now that we have a working model which can make decision for us, we decided to implement the model in a live trading environment. One platform stood out to us was [Alpaca](https://alpaca.markets/), which has an a robust API and offers paper trading feature. 

We pipe our code to Alpaca API, then schedule to run every day during trading hours with hourly data refresh. The output will show a table showing number of shares to buy or sell in the universe, which we will be pick up to trade using Alpaca.
