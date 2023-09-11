# LayerZero.t.sol

> 💻 The code corresponding to this page can be found on Github at [OmniCounter.t.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/Applications/LayerZero/lzMockEndpoint/OmniCounter.t.sol)💻

## Conceptual detour

Before diving into the test file, let us take a step back and think about what we are trying to do here.

We are trying to mock the behavior of the LayerZero protocol on our local testnet. But does it not require a minimum of two blockchains to implement any actual cross-chain communication?
Good point. 
So let us think about it a little more.

Here is what the flow of a cross-chain message looks like:
**Smart contract on Chain A -> LayerZero Endpoint on Chain A -> Relayer + Oracle -> LayerZero Endpoint on Chain B -> Smart contract on Chain B**

Now, we can't really mock the behavior of the relayer and the oracle, but how do we set up a mock endpoint, that can impersonate the cross-chain behavior pattern of two endpoints on a single network?

How about this:

1. Smart contract number 1 on local network calls `send()` function on mock endpoint.
2. Mock endpoint does NOT check the message's validity in any way.
3. The mock endpoint simply calls the `_nonblockingLzReceive` on smart contract number 2.

This process happens in reverse when smart contract number 2 calls the `send()` function on the mock endpoint.
If our message passes from:

**Smart contract number 1 -> Mock Endpoint -> Smart contract number 2**, and vice-versa, then we can be reasonably assured that our contracts are correctly configured to interact with the LayerZero protocol.

## Setting up the test file

Create a file named `OmniCounter.t.sol` inside your Foundry project.

To test our VRF contract using `LZEndpointMock`, set up the test file with the following imports:

```cpp
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.19;

//Imports to use Foundry's testing and logging suite
import {Test, console2} from "forge-std/Test.sol";

//Imports to use the LayerZero contracts
import {LZEndpointMock} from "@layerzero-contracts/mocks/LZEndpointMock.sol";
import {OmniCounter_Sepolia} from "./OmniCounter_Sepolia.sol";
import {OmniCounter_Mumbai} from "./OmniCounter_Mumbai.sol";
```

Next, initialize a contract named `OmniCounterTest`:

```cpp
contract OmniCounterTest is Test {

}
```

Next, let us set up the state variables:

```cpp

    LZEndpointMock public lzEndpointMock;

    OmniCounter_Sepolia public omniCounter_Sepolia;
    OmniCounter_Mumbai public omniCounter_Mumbai;

    //Assigning a random ChainId to the local network
    uint16 public constant ChainId = 1221;
```

Let us set up the initial state using the `setUp()` function:

```cpp
    function setUp() public {

        vm.deal(address(0x1), 100 ether);
        vm.deal(address(0x2), 100 ether);
        vm.deal(address(0x3), 100 ether);

        vm.prank(address(0x1));
        lzEndpointMock = new LZEndpointMock(ChainId);

        vm.prank(address(0x2));
        omniCounter_Sepolia = new OmniCounter_Sepolia(address(lzEndpointMock));

        vm.prank(address(0x3));
        omniCounter_Mumbai = new OmniCounter_Mumbai(address(lzEndpointMock));

        vm.deal(address(lzEndpointMock), 100 ether);
        vm.deal(address(omniCounter_Sepolia), 100 ether);
        vm.deal(address(omniCounter_Mumbai), 100 ether);


        vm.startPrank(address(0x1));
        lzEndpointMock.setDestLzEndpoint(address(omniCounter_Sepolia), address(lzEndpointMock));
        lzEndpointMock.setDestLzEndpoint(address(omniCounter_Mumbai), address(lzEndpointMock));
        vm.stopPrank();
        
        bytes memory omniCounter_Mumbai_Address = abi.encodePacked(uint160(address(omniCounter_Mumbai)));
        bytes memory omniCounter_Sepolia_Address = abi.encodePacked(uint160(address(omniCounter_Sepolia)));


        vm.prank(address(0x2));
        omniCounter_Sepolia.setTrustedRemoteAddress(ChainId, omniCounter_Mumbai_Address);

        vm.prank(address(0x3));
        omniCounter_Mumbai.setTrustedRemoteAddress(ChainId, omniCounter_Sepolia_Address);
```

This might seem overwhelming, but trust me, its' not. Let us break this down:

1. The `deal` cheatcode is provided to us by Forge, and we can use it to bootstrap any address with some ETH.
2. The `prank` cheatcode is also provided to us by Forge, and we can use it to impersonate any address. Any function call immediately proceeding a `prank` call will be executed as if it was called by the impersonated address.
3. We initialize a new instance of the `LZEndpointMock` contract, followed by the initialization of the two OmniCounter contracts with the address of the mock endpoint.
Note that all 3 of the contracts have different owners.
4. We then transfer some ETH to the mock endpoint and the two OmniCounter contracts using the `deal` cheatcode.
5. The `setDestLzEndpoint` is used to connect the OmniCounter contracts to the mock endpoint.
6. The `setTrustedRemoteAddress` function is defined in the abstract contract we used to write our own contract, and is used to connect the two OmniCounter contracts to each other. Only contracts that have marked each other as trusted can send messages between them.

> 📝 Note:
> Confused by `address(0x1)`, `address(0x2)` and `address(0x3)`?
> address(0x1) is the Ethereum address that corresponds to the hexadecimal value 0x0000000000000000000000000000000000000001.
> Any number can be typecasted into a hexadecimal value, which in turn can be padded by a number of zeroes to make it 20 bytes long. 

### Sepolia to Mumbai

Let us now write a test function to check if we can increment the `counter` variable on the Mumbai testnet by calling the `incrementCounter` function on the Sepolia testnet.

```cpp
    /**
     * @dev Tests the counter increment from Sepolia to Mumbai.
    */
    function test_SepoliaToMumbai() public {

        uint counter_initial = omniCounter_Mumbai.counter();

        vm.deal(address(0x10), 100 ether);
        vm.prank(address(0x10));
        omniCounter_Sepolia.incrementCounter{value: 1 ether}(ChainId);
        assertEq(omniCounter_Mumbai.counter(), counter_initial+1);
    }
```

This contract does a few things:

1. First we record the initial value of the `counter` variable on the Mumbai testnet.
2. Then we use the `deal` cheatcode to bootstrap a new address with some ETH.
3. We then impersonate this address using the `prank` cheatcode.
4. We then call the `incrementCounter` function on the Sepolia testnet, and send 1 ETH along with the function call.
5. Finally, we check if the value of the `counter` variable on the Mumbai testnet has increased by 1.

The test passes if the counter value increases by 1, otherwise it fails.

### Mumbai to Sepolia

We can write a similar test function to check if we can increment the `counter` variable on the Sepolia testnet by calling the `incrementCounter` function on the Mumbai testnet.

```cpp
    /**
     * @dev Tests the counter increment from Mumbai to Sepolia.
    */
    function test_MumbaiToSepolia() public {

        uint counter_initial = omniCounter_Sepolia.counter();

        vm.deal(address(0x10), 100 ether);
        vm.prank(address(0x10));
        omniCounter_Mumbai.incrementCounter{value: 1 ether}(ChainId);
        assertEq(omniCounter_Sepolia.counter(), counter_initial+1);
    }
```

To run the tests, run the following command in your terminal:

```bash
forge test --match-path src/Applications/LayerZero/lzMockEndpoint/OmniCounter.t.sol -vvvv
```

Please note that the exact command will vary depending on your directory structure.
