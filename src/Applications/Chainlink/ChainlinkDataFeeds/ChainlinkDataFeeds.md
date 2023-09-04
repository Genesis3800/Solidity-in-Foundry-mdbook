# Chainlink Data Feeds

[Chainlink's data feeds](https://docs.chain.link/data-feeds#types-of-data-feeds) are a collection of data points from Chainlink's decentralized oracle network.

Chainlink offers a wide variety of data feeds, but in this tutorial we will be going over their price feeds service.
Each Chainlink price feed returns the relative price of two assets, such as ETH/USD or BTC/USD.

We will write a simple smart contract that consumes Chainlink's data feeds, and then a Foundry test for that smart contract.

Briefly, here is how Chainlink's price feeds service works:

1. Chainlink has a network of off-chain nodes that fetch the latest prices of different assets and arrive at a consensus in real-time. These prices are fed into an on-chain aggregator contract.

2. Each price feed has its' own aggregator contract.

3. To query the latest relative price of any two assets, we simply need to call the `latestRoundData()` from the designated contract of that price feed.