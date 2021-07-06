# Automated Equity Trading using Deep Reinforcement Learning

## Overview

This project was inspired by a paper on automated trading using AI, ["Deep Reinforcement Learning for Automated Stock Trading: An Ensemble Strategy"](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3690996), code of which can be found here on [Github](https://github.com/AI4Finance-LLC/FinRL). The model adopted an ensemble approach using three actor-critic algorithms: Proximal Policy Optimization (PPO), Advantage Actor Critic (A2C), and Deep Deterministic Policy Gradient (DDPG) with an incorporation of turbulence index to address the analyst's risk aversion during market turbulence.

## Our tweaks to the model

We were interested in incorporating this model into live trading environment to test how well it performs in real-life setting, hence the start of this project. We did, however, made some changes in the library and in the model to adapt to our personal investment outlook:

- Used S&P 500 as our stock universe instead of Dow Jones for a more diverse set of equities
- Added more technical indicators to the state space, including Bollinger Bands (upper & lower), SMA 30, and SMA 60 to existing list of MACD, RSI, CCI, and DX
- Our initial back test showed sharp drawdowns during market turbulences, much more volatile than the paper's model. Hence, we pushed the trading start time from 2009 back to 2000. Our model was able to learn from more market cycles, resulting in improved Sharpe Ratio and tempered volatility during market crash periods. The trained agents will then be exported to be then used to train and validate new data coming in.
- Training period started from today minus 126 days (63 days, which is equivalent to 1 quarter, for validation period plus 63 days for rebalancing window) to today minus 63 days (validation period)
- Validation period started from training period end date (today minus 63 days) to today. Thus, trading decisions will be renewed everytime model is refreshed and learn from dynamically changing most recent data
- Initial turbulence threshold was set to account for data from 2000/01/01 to 06/23/2021
- Training and validation phases will incorporate existing trained models (like mentioned above) to make decision based on past learning behavior for new data.

## Live Trading Environment

Now that we have a working model which can make decision for us, we decided to implement the model in a live trading environment. One platform stood out to us was [Alpaca](https://alpaca.markets/), which has an a robust API and offers paper trading feature. 

We pipe our code to Alpaca using their API, then schedule to run every day during trading hours with hourly refresh. The output will show a table showing number of shares to buy or sell in the universe, which will be picked up by Alpaca to perform trading.
