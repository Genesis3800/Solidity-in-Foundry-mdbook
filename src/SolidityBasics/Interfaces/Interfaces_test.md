# Interfaces.t.sol

> 💻 The code corresponding to this page can be found on Github at [Interfaces.t.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/SolidityBasics/Interfaces/Interfaces.t.sol)

As usual, start by importing all the required files:

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.19;

import {Test, console2} from "forge-std/Test.sol";
import {SimpleAddition} from "./SimpleAddition.sol";
import {UsingInterface} from "./UsingInterface.sol";

contract UsingInterface_test is Test {

}
```

Next, define the `setup()` function that sets the initial state for each test function:

```solidity
    SimpleAddition public simpleAddition;
    UsingInterface public usingInterface;

    function setUp() public {
        
        simpleAddition = new SimpleAddition();
        usingInterface = new UsingInterface(address(simpleAddition));
    }
```

Note that we use the address of the `SimpleAddition` contract to initialize the `UsingInterface` contract.
Next, let us write two test functions to check if we can change the values the state variables:

```solidity
    function test_setA() public {
        usingInterface.setA(1);
        assertEq(simpleAddition.a(), 1, "Value of `a` in SimpleAddition should be 1");
    }

    function test_setB() public {
        usingInterface.setB(2);
        assertEq(simpleAddition.b(), 2, "Value of `b` in SimpleAddition should be 2");
    }
```

Finally, let us write test functions to check if we can call the addition functions defined in the `SimpleAddition` contract. We want to make sure that the 

```solidity
    // The values of `a` and `b` were set as 10 and 20 respectively
    // in the `SimpleAddition` contract. Thus, the sum should be 30
    // if we call the `returnSumOfStateVariables()` function without
    // changing the values of `a` and `b` first.
    function test_returnSumOfStateVariablesWithoutChange() public {
        assertEq(usingInterface.returnSumOfStateVariables(), 30, "sum of state variables should be 30");
    }

    function test_returnSumOfStateVariablesWithChange() public {
        usingInterface.setA(1);
        usingInterface.setB(2);
        assertEq(usingInterface.returnSumOfStateVariables(), 3, "sum of state variables should be 3");
    }

    function test_returnSumOfLocalVariables() public {
        assertEq(usingInterface.returnSumOfLocalVariables(5, 10), 15, "sum should be 3");
    }
```

Run the tests and see if they pass.
