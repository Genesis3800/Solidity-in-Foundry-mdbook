# ChainlinkDataFeeds.sol

> 💻 The code corresponding to this page can be found on Github at [DataFeeds.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/Applications/Chainlink/ChainlinkDataFeeds/ChainlinkDataFeeds.sol) 💻

Before getting started with the actual code, we need to install the dependencies for this project. Make sure you have a Foundry project initialized before moving on.

## Installing dependencies

Dependencies in a Foundry project are managed using [git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) by default. 
For this project, we will be using a slimmed down version of Chainlink's official smart contracts repo.

To download the repo into the lib directory, run:

```sh
forge install https://github.com/smartcontractkit/chainlink-brownie-contracts/
```

**or simply**

```sh
forge install chainlink-brownie-contracts
```

> 📝  **Note:**
> In my experience, Forge may sometimes install outdated versions of the dependencies. Run `forge update` after installing new
> dependencies to make sure you have the latest versions.

## Remapping dependencies

While working with installed dependencies, we may need to explicitly tell Forge where to look for the code.
This can be done by defining your remappings inside a separate file.
To create this file, run:

```sh
forge remappings > remappings.txt
```

This will create a `remappings.txt` file in the root directory of your project.
For now, delete everything inside the default remappings file and add the following two remappings inside:

```sh
forge-std/=lib/forge-std/src/
@chainlink/=lib/chainlink-brownie-contracts/contracts/src/
```

The `forge-std` remapping is used to tell Forge where to look for the Forge standard library. The `@chainlink` remapping abstracts away the path to the Chainlink contracts.

## Writing the smart contract

To get started, create a new file called `DataFeeds.sol` inside your Foundry project.

As mentioned earlier, to use Chainlink's price feeds service, you simply need to query the designated aggregator contract for the specific price feed you want to use.
Since all of Chainlink's pricde feeds contracts are built on top of a single interface, we can use the same interface to interact with all of them.

To get started, import the `AggregatorV3Interface` into your contract code:

```cpp
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import "@chainlink/v0.8/interfaces/AggregatorV3Interface.sol";
```

Next, initialize a contract named `ChainlinkDataFeeds `:

```cpp
contract ChainlinkDataFeeds {
    
}
```

Inside the contract:

1. Declare a variable of the type `AggregatorV3Interface`.
2. Inside the constructor, initialize the new variable by passing the address of the aggregator contract you want to use.

```cpp
    AggregatorV3Interface internal immutable priceFeed;

    constructor(address _priceFeed) {
        priceFeed = AggregatorV3Interface(_priceFeed);
    }
```

Finally, define a function named `getLatestPrice()` that performs an external call to the `latestRoundData()` function of the aggregator contract:

```cpp
    function getLatestPrice() public view returns (uint80 roundID, int price) {
        (roundID, price ,,,) = priceFeed.latestRoundData();
        return (roundID, price);
    }
```

While we are only dealing with two values, the `latestRoundData()` function actually returns:

1. `roundId`: Chainlink's data feeds are updated with each 'round'. This value is the ID of the current round.
2. `answer`: The actual data returned by the data feed. While we are working with price feeds, this value could be anything, depending on the actual data feed you are working with.
3. `startedAt`: Unix timestamp of when the round started.
4. `updatedAt`: Unix timestamp of when the round was updated.
5. `answeredInRound`: **Deprecated**

And that is it for the contract. Before deploying it though, we need to be familiar with a few concepts related to Chainlink's price feeds:

1. The complete reference for each price feed offered by Chainlink across all of its' networks can be found [in their docs](https://docs.chain.link/data-feeds/price-feeds/addresses).
2. Different price feeds may vary in their level of precision. For example, on ETH mainnet, the price feed for `ETH/USD` returns the price in 8 decimals, while the price feed for `BTC/ETH` returns the price in 18 decimals.
The result needs to be dividied by 10 to the power of the number of decimals to get the actual price.
3. Each price feed has a set '*heartbeat*'. This is the default time interval between each update of the price feed.
4. Each price also has a deviation threshold. If the price deviates from the previous price by more than this percentage, it is updated immediately.
