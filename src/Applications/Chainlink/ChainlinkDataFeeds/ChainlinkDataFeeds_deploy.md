# ChainlinkDataFeeds.s.sol

> 💻 The code corresponding to this page can be found on Github at [ChainlinkDataFeeds.s.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/Applications/Chainlink/ChainlinkDataFeeds/ChainlinkDataFeeds.s.sol)

The best way to deploy contracts using Foundry is to use the write a simple deploy script.
However, we first need to set up a dotenv file to store our environment variables.

At the root of your project, create a file named `.env`.
Fill out your environment variables like this:

```sh
# Alchemy RPC URLs
SEPOLIA_RPC_URL=

# Private Key
PRIVATE_KEY=

# Etherscan and Polygonscan API keys
ETHERSCAN_API_KEY=
```

1. You can get RPC URLs from [Alchemy](https://www.alchemy.com/), or any other RPC provider. You can also use a public RPC URL.
2. Make sure to use the private key of a wallet that has some Sepolia ETH. You can get some from [Alchemy's Sepolia faucet](https://sepoliafaucet.com/).
3. Get your Etherscan API key from [Etherscan's website](https://etherscan.io/apis).

Once your environment variables are set up, run `source .env` to load them into your shell.
Let us now write a script to deploy the data feeds contract.

> 📝 **Note:**
>  **This is really, really important.** Whatever happens, make sure to **NOT** push your env variables to Github.
> To make sure that doesn't happen, add `.env` to your `.gitignore` file.
> Please note that even using a normal dotenv file is not recommended behaviour.
> Ideally you should be using a hardware wallet, and/or an encrypted keystore.
> But for the sake of simplicity, I'll be using a dotenv file throughout.

Create a file named `ChainlinkDataFeeds.s.sol`, and initialize a deployment contract like this:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.19;

import "forge-std/Script.sol";
import {ChainlinkDataFeeds} from "./ChainlinkDataFeeds.sol";

contract DeployDataFeed is Script {

}
```

This contract is executed using the `forge script` command. Next, within the `DeployDataFeeds` contract, initialize an instance of the data feeds contract, and define a function named `run()`:

```solidity
 ChainlinkDataFeeds chainlinkDataFeeds;

    function run() external {
        
        // Using the envUint cheatcode we can read some env variables
        uint256 PrivateKey = vm.envUint("PRIVATE_KEY");

        // Anything within the broadcast cheatcodes is executed on-chain
        vm.startBroadcast(PrivateKey);
        chainlinkDataFeeds = new ChainlinkDataFeeds(0x694AA1769357215DE4FAC081bf1f309aDC325306);
        vm.stopBroadcast();
    }
```

A few things happen here:

1. The `run()` function serves as the default entry point for the script.
2. We can use the `envUint()` cheatcode to read environment variables from the `.env` file. This way we don't have to pass the private key as a command line argument.
3. The `startBroadcast()` and `stopBroadcast()` cheatcodes are used to execute the code on-chain. Anything within these two cheatcodes is executed on-chain.
4. Finally, the new instance of the data feeds contract is passed an address as an argument. This address is the address of the aggregator contract that serves the ETH/USD price feed on the Sepolia testnet.

Save everything and run `forge build` to make sure everything compiles correctly.
To run the script, use the `forge script` command like this:

```sh
forge script src/Applications/Chainlink/ChainlinkDataFeeds/ChainlinkDataFeeds.s.sol:DeployDataFeed \
--rpc-url $SEPOLIA_RPC_URL \
--broadcast -vvvv
```

The exact params may of course differ depending on your setup.
To verify the contract once it is deployed, run:

```sh
forge verify-contract <Your contract address> \
--chain-id 11155111 \
--num-of-optimizations 200 \
--watch --compiler-version v0.8.19+commit.7dd6d404 \
--constructor-args $(cast abi-encode "constructor(address)" 0x694AA1769357215DE4FAC081bf1f309aDC325306) \
src/Applications/Chainlink/ChainlinkDataFeeds/ChainlinkDataFeeds.sol:ChainlinkDataFeeds \
--etherscan-api-key $ETHERSCAN_API_KEY
```

> 📝 **Note:**
> It is possible to verify the contract your contract at the time of deployment by passing the `--verify` flag
> to the `forge script` command. However, this way of verification fails more often than not in my experience.
> You can read more about this in [Foundry's docs](https://book.getfoundry.sh/reference/forge/forge-verify-contract?highlight=--verify#examples).