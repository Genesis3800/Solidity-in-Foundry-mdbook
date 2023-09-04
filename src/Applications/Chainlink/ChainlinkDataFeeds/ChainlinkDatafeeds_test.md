# ChainlinkDataFeeds.t.sol

> 💻 The code corresponding to this page can be found on Github at [DataFeeds.t.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/Applications/Chainlink/ChainlinkDataFeeds/ChainlinkDataFeeds.t.sol) 💻

Create a file named `ChainlinkDataFeeds.t.sol`. To check if our contract is correctly configured to read from Chainlink's aggregator contracts, we will take the help of the `MockV3Aggregator` contract.

This contract will mock the behavior of the aggregator contracts, and will allow us to test our contract without having to deploy it to a testnet.
If our contract can read from this mock aggregator contract, it will likely be able to read from the real aggregator contracts as well, since they are built on top of the same interface.

To get started, create a file named `DataFeeds.t.sol`. Inside the file, make the following imports:

```cpp
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.19;

import "forge-std/Test.sol";
import {ChainlinkDataFeeds} from "./ChainlinkDataFeeds.sol";
import {MockV3Aggregator} from "@chainlink/v0.8/tests/MockV3Aggregator.sol";
```

Next, initialize a contract named `ChainlinkDataFeeds_test`:

```cpp
contract ChainlinkDataFeeds_test is Test {

}
```

The `MockV3Aggregator` contract needs to be initialized with some dummy data. Declare the following state variables inside the `ChainlinkDataFeeds_test` contract:

```cpp
    // Configuring the base data for the mock aggregator
    uint8 public _decimals = 8;
    int256 public _initialAnswer = 10**18;

    // Initializing the contract instances
    ChainlinkDataFeeds public chainlinkDataFeeds;
    MockV3Aggregator public mockV3aggregator;
```

The `setUp()` function is a special function that if often used while writing tests in Foundry.
This function is executed by Forge before every single test function in a file.
This allows us to set up an initial state that we can use to test our contracts on.

```cpp
    function setUp() public {

        mockV3aggregator = new MockV3Aggregator(_decimals, _initialAnswer);
        chainlinkDataFeeds = new ChainlinkDataFeeds(address(mockV3aggregator));
    }
```

Finally, we define a function named `testgetLatestPrice()`.
This function will call the `getLatestPrice()` function on our contract, which in turn calls the `latestRoundData()` function on the mock aggregator contract.
We then check if the values returned by the `getLatestPrice()` function are the same as the values we initialized the mock aggregator contract with.

If the values are in-line with what we expect, we can be certain that our contract is correctly configured to read from Chainlink's aggregator contracts.

```cpp
    function testgetLatestPrice() public {

        (uint80 roundID, int256 price) = chainlinkDataFeeds.getLatestPrice();
        
        assertEq(price, _initialAnswer);
        assertEq(roundID, 1);
    }
```
