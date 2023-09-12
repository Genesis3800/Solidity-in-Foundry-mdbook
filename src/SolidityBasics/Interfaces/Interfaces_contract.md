# UsingInterface.sol

> 💻 The code corresponding to this page can be found on Github at [UsingInterface.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/SolidityBasics/Interfaces/UsingInterface.sol)

Let us write the `UsingInterface.sol`, that will use the `Interface_SimpleAddition` to call functions on the `SimpleAddition` contract.

```cpp
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

/* Note that we only import the interface, not the actual contract
   we will be calling.
*/
import {Interface_SimpleAddition} from "./Interface_SimpleAddition.sol";

contract UsingInterface {

    Interface_SimpleAddition public IsimpleAddition;

    constructor(address _simpleAdditionAddress) {
        IsimpleAddition = Interface_SimpleAddition(_simpleAdditionAddress);
    }

    function setA(uint256 _a) public {
        IsimpleAddition.setA(_a);
    }

    function setB(uint256 _b) public {
        IsimpleAddition.setB(_b);
    }

    function returnSumOfStateVariables() public view returns (uint256) {
        return IsimpleAddition.returnSumOfStateVariables();
    }

    function returnSumOfLocalVariables(uint256 _a, uint256 _b) public view returns (uint256) {
        return IsimpleAddition.returnSumOfLocalVariables(_a, _b);
    }

}
```

A few notes:

1. The `UsingInterface` contract takes the address of the `SimpleAddition` contract as a constructor param. This is needed because the actual functions will be called on an already deployed contract.
2. An instance of the `Interface_SimpleAddition` interface can then be used to call functions on the `SimpleAddition` contract.
3. If the function declarations in the interface are incorrect, the calls to the underlying contract will fail.

In the final section, we will write a few Foundry tests to check if `UsingInterface` can call functions on `SimpleAddition`.
