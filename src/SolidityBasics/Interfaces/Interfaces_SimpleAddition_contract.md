# SimpleAddition.sol

> 💻 The code corresponding to this page can be found on Github at [SimpleAddition.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/SolidityBasics/Interfaces/SimpleAddition.sol)

We will write a simple smart contract that has two addition functions:

1. An addition function that takes two integers and returns their sum.
2. An addition function that returns the sum of the two public state variables.

In addition, we will have two simple functions that can be used to set new values for the aforementioned state variables.

```cpp
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

/**
 * @title SimpleAddition
 * @dev SimpleAddition contract is used to demonstrate the use of interfaces.
 * We will write an interface for this contract.
 */
contract SimpleAddition {

    uint256 public a =10; uint256 public b =20;

    function setA(uint256 _a) public {
        a = _a;
    }

    function setB(uint256 _b) public {
        b = _b;
    }

    function returnSumOfStateVariables() public view returns (uint256) {
        return a + b;
    }

    /**
     * @dev Both public and external functions can be called by other contracts.
     */
    function returnSumOfLocalVariables(uint256 _a, uint256 _b) external pure returns (uint256) {
        return _a + _b;
    }

}
```

1. The first addition function will not take any params and will simply return the sum of the two state variables.
2. The second addition function will return the sum of the two params passed to it.
3. Note that both public and external functions can be called using a contract's interface. Also, variables cannot be marked external.

In the next section, we will write an interface for this contract.
