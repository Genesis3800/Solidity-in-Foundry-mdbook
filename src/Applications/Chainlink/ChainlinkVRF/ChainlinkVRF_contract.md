# ChainlinkVRF.sol

> 💻 The code corresponding to this page can be found on Github at [ChainlinkVRF.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/Applications/Chainlink/ChainlinkVRF/ChainlinkVRF.sol) 💻

## Creating a VRF subscription

Unlike the price feeds service, Chainlink VRF is not free to use.
Each randomness request costs a certain amount of LINK tokens.

There are two ways to pay for a VRF request:

1. **Direct Funding:** Your smart contract directly pays a small amount of LINK tokens with each request.
2. **Subscription Method:** You fund a single subscription with LINK tokens, and add your smart contracts as approved consumers of that subscription. Each time your smart contract makes a randomness request, the subscription is charged a small amount of LINK tokens.

We will be using the subscription method in this tutorial.

These are the steps to set up a VRF subscription:

1. Make sure you have some LINK tokens in an EOA. You can get a few from [Chainlink's faucet](https://faucets.chain.link/).
2. Go to [vrf.chain.link](https://vrf.chain.link/) and create a new subscription. I will be working with the Sepolia testnet.
3. Fund your subscription with some LINK tokens(50 should be more than enough).

We are now ready to start writing our smart contract.

## Writing the smart contract

To get started, create a new file called `ChainlinkVRF.sol` inside your Foundry project.

We need to import two things into our contract:

1. `VRFCoordinatorV2Interface`: Each network supported by Chainlink has an on-chain coordinator contract that handles all VRF requests. use this interface to interact with the coordinator contract.
2. `VRFConsumerBaseV2`: This is an abstract contract, which means this is an incomplete contract with at least one of its functions left unimplemented. We will inherit from this contract and implement the missing function, `fulfillRandomWords()`.

```solidity 
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import "@chainlink/v0.8/interfaces/VRFCoordinatorV2Interface.sol";
import "@chainlink/v0.8/VRFConsumerBaseV2.sol";
```

Next, let us initialize a contract named `ChainlinkVRF`:

```solidity
contract ChainlinkVRF is VRFConsumerBaseV2 {

}
```

To send a randomness request to Chainlink VRF, we need to configure a few variables. Let us take a look:

- `keyHash`: Each `keyHash` is a hexadecimal value that represents a '*gas lane*'. Each gas lane costs a different amount of gas.
- `subId`: The subscription ID of the subscription we created earlier.
- `minimumRequestConfirmations`: The number of blocks you would like the off-chain node to wait before fulfilling your request. The higher this number, the more secure your randomness will be.
- `callbackGasLimit`: How much gas would you like to be used on your callback function.
- `numWords`: The number of `uint256` values you would like to receive. We will be requesting 1 word.

> 📝  **Note:**
> All of these values will differ depending on the network you are working with. You can find the technical reference for all supported
> networks [here](https://docs.chain.link/vrf/v2/subscription/supported-networks).

Let us configure these variables:

```solidity
    VRFCoordinatorV2Interface private CoordinatorInterface;
    uint64 private _subscriptionId;
    bytes32 private constant KEY_HASH = 0x474e34a077df58807dbe9c96d3c009b23b3c6d0cce433e59bbf5b34f823bc56c;
    uint32 private constant CALLBACK_GAS_LIMIT = 100000;
    uint16 private constant BLOCK_CONFIRMATIONS = 10;
    uint32 private constant NUM_WORDS = 1;

    // Variable to store the generated random number
    uint256 public number;
```

The `CoordinatorInterface` and `_subscriptionId` variables will be initialized in the constructor.
The `number` variable will store the generated random number.

The constructor can be initialized as follows:

```solidity
    constructor(uint64 subscriptionId, address vrfCoordinatorV2Address) VRFConsumerBaseV2(vrfCoordinatorV2Address) {
        _subscriptionId = subscriptionId;
        CoordinatorInterface = VRFCoordinatorV2Interface(vrfCoordinatorV2Address);
    }

```

Let us now define a function named `useChainlinkVRF()` that will send a randomness request to the Chainlink VRF coordinator contract.
This function will send a randomness request to the coordinator contract, and return a `requestId` that will be used to identify the request.

```solidity
     function useChainlinkVRF() public returns (uint256 requestId) {
        requestId = CoordinatorInterface.requestRandomWords(
            KEY_HASH,
            _subscriptionId,
            BLOCK_CONFIRMATIONS,
            CALLBACK_GAS_LIMIT,
            NUM_WORDS
        );
        return requestId;
    }
```

Lastly, we need to define the `fulfillRandomWords()` function. This function will be called by the Chainlink VRF coordinator contract once the randomness request has been fulfilled by the off-chain Chainlink node.

We can modulo the huge random number we get back to trim it down to a desired range.
Based on the number we get back, we will set the `number` variable to 1, 2, or 3.

```solidity
    function fulfillRandomWords(uint256 requestId, uint256[] memory randomWords) internal override {

        // To get a random number b/w 1 and 100 inclusive
        uint256 randomNumber = (randomWords[0] % 100) + 1;

        if(randomNumber == 100){
            number = 1;
        } else if(randomNumber % 3 == 0) {
            number = 2;
        } else {
            number = 3;
        }
    }
```

And that's it for now.
By calling the `useChainlinkVRF()` function on our contract, a user can get back a random number b/w 1 and 3 with a varying degree of probability.

In the next section, we will use the `VRFCoordinatorV2Mock` to write a preliminary test for our VRF contract.