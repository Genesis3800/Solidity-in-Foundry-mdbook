# Interface_SimpleAddition.sol

> 💻 The code corresponding to this page can be found on Github at [Interface_SimpleAddition.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/SolidityBasics/Interfaces/Interface_SimpleAddition.sol)

Let us write the interface for the `SimpleAddition` contract.

A few points to note:

1. Interfaces are initialized using the `interface` keyword.
2. All function declarations must be marked external, and must configure the param and return types exactly as they are in the contract.
3. No function will have any sort of definition.  

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

/**
 * @title Interface_SimpleAddition
 * @dev All function declarations in an interface must be external.
 */
interface Interface_SimpleAddition {

    function setA(uint256 _a) external;

    function setB(uint256 _b) external;

    function returnSumOfStateVariables() external view returns (uint256);

    function returnSumOfLocalVariables(uint256 _a, uint256 _b) external pure returns (uint256);

}
```

In the next section, we will write a contract that uses this interface to call functions on the `SimpleAddition` contract.