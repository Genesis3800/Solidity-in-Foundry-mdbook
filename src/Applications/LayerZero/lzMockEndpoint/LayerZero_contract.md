# LayerZero.sol

> 💻 The code corresponding to this page can be found on Github at [OmniCounter_Sepolia.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/Applications/LayerZero/lzMockEndpoint/OmniCounter_Sepolia.sol), and [OmniCounter_Mumbai.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/Applications/LayerZero/lzMockEndpoint/OmniCounter_Mumbai.sol) 💻

Before we start writing our smart contract, we need to install two dependencies:

1. [LayerZero-Labs/solidity-examples](https://github.com/LayerZero-Labs/solidity-examples/): This repo contains a bunch of examples put together by the LayerZero team, as well as the mock endpoint contract that we will be using in this tutorial.
2. [openzeppelin-contracts](https://github.com/OpenZeppelin/openzeppelin-contracts): This repo is used as a dependency by the LayerZero repo, and needs to be installed separately.

```bash
forge install LayerZero-Labs/solidity-examples/ OpenZeppelin/openzeppelin-contracts@v4.4.1
```

> 📝  **Note:**
> Forge allows us to install multiple dependencies in a single command. It also allows us to choose the exact version of any dependency we want to install. In this
> case, we are installing the `v4.4.1` version of `openzeppelin-contracts`.
> The latest version of the OpenZeppelin contracts library has some breaking changes, and breaks the code. Hence, we are working with the same version that the guys
> over at LayerZero are working with.
> Forge gives us quite a few options to configure the exact versioning as we want. You can read more about it
> [in the Foundry book](https://book.getfoundry.sh/reference/forge/forge-install?highlight=forge%20insta#description).

Insert these two remappings inside the `remappings.txt` file:

```bash
@layerzero-contracts/=lib/solidity-examples/contracts/
@openzeppelin/=lib/openzeppelin-contracts/
```
You need to create two files:

- `OmniCounter_Sepolia.sol`
- `OmniCounter_Mumbai.sol`

Let us start with `OmniCounter_Sepolia.sol`:

Import the `NonblockingLzApp.sol` file. This file contains an abstract contract named `NonblockingLzApp`.
We will implement the `_nonblockingLzReceive()` function in our contract, that was left undefined in the abstract contract.

```cpp
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.19;

// Importing the base contract for LayerZero applications
import "@layerzero-contracts/lzApp/NonblockingLzApp.sol";
```

Initialize the `OmniCounter_Sepolia` contract:

```cpp
/**
 * @title OmniCounter_Sepolia
 * @dev This contract represents the OmniCounter application on the Sepolia testnet.
 * It allows for incrementing a counter and sending a payload to another chain.
 */
contract OmniCounter_Sepolia is NonblockingLzApp {

}
```

We will define two state variables:

1. `PAYLOAD`: This is the payload that we want to send to the Mumbai testnet. We will set it to `Sepolia_to_Mumbai`. Note that the payload might need to be dynamic depending on the exact logic of the contract. For our needs, a constant payload will suffice.
2. `counter`: We want this variable to be incremented by 1 everytime the `incrementCounter` function is called on the Mumbai testnet.

We will also initialize the two constructors with the address of the on-chain LayerZero endpoint for Sepolia.

```cpp
    bytes public constant PAYLOAD = "Sepolia_to_Mumbai";
    uint public counter;

    constructor(address _lzEndpoint) NonblockingLzApp(_lzEndpoint) {}
```

Let us define the actual `incrementCounter` function. Whenever this function is called, it performs an external call to the LayerZero endpoint, calling the `send()` function on it.
Let us look at the parameters of the `send()` function:

1. `_dstChainId`: This is the chain ID of the destination chain. In our case, it is the Mumbai testnet.
2. `PAYLOAD`: This is the payload/message that we want to send to the destination chain. In our case, it is `Sepolia_to_Mumbai`.
3. `msg.sender`: We need to pay some gas to send a message to the on-chain endpoint. The gas sent is usually higher than necessary to ensure a smooth sailing for our payload. This is the address that the excess gas will be refunded to.
4. `address(0x0)`: This param is not in use right now. Might be used in the future.
5. `bytes("")`: This param is used to tweak the default settings of our contract's interaction with the LayerZero protocol. We will not be using it in this tutorial.
6. `msg.value`: The user calling `incrementCounter` needs to send some ETH along with the function call. This param keeps a track of the amount of ETH sent.

```cpp
    function incrementCounter(uint16 _dstChainId) public payable {
        _lzSend(_dstChainId, PAYLOAD, payable(msg.sender), address(0x0), bytes(""), msg.value);
    }
```

Next, we implement the `_nonblockingLzReceive` function. This function is called by the LayerZero endpoint when it receives a message that needs to be delivered to this contract.
We an include whatever logic we want executed in response to the message receival in the function definition. In this case, we just increment the value of the counter variable.

```cpp
        function _nonblockingLzReceive(uint16, bytes memory, uint64, bytes memory) internal override {
        counter += 1;
    }
```

And that is it for our `OmniCounter_Sepolia.sol` contract.
Please note that this is an exceedingly simple implementation of the LayerZero protocol. This code is ***NOT*** meant to be used in production.

The `OmniCounter_Mumbai.sol` contract is almost identical. Paste the following code inside it:

```cpp
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.19;

// Importing the base contract for LayerZero applications
import "@layerzero-contracts/lzApp/NonblockingLzApp.sol";

/**
 * @title OmniCounter_Mumbai
 * @dev This contract represents the OmniCounter application on the Mumbai testnet.
 * It allows for incrementing a counter on its' analogous contract deployed on the Sepolia testnet.
 */
contract OmniCounter_Mumbai is NonblockingLzApp {
    bytes public constant PAYLOAD = "Mumbai_to_Sepolia";
    uint public counter;

    constructor(address _lzEndpoint) NonblockingLzApp(_lzEndpoint) {}

    function incrementCounter(uint16 _dstChainId) public payable {
        _lzSend(_dstChainId, PAYLOAD, payable(msg.sender), address(0x0), bytes(""), msg.value);
    }

    function _nonblockingLzReceive(uint16, bytes memory, uint64, bytes memory) internal override {
        counter += 1;
    }
}
```
