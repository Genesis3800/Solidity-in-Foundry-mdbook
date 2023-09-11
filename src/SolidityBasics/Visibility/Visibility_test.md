# Visibility.t.sol

> 💻 The code corresponding to this page can be found on Github at [Visibility.t.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/SolidityBasics/Visibility/Visibility.t.sol)

As usual, create a new file and import the required Solidity files to initialize the test contract:

```cpp
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.19;

import {Test, console2} from "forge-std/Test.sol";
import "./Visibility.sol";

contract Visibility_test is Test {

}
```

Now, define the `setup()` function that sets the initial state for each test function:

```cpp
    Child child;

    function setUp() public {
        child = new Child();
    }
```

Next, make sure we can call the internal and public addition functions defined in the parent contract, from within the child contract:

```cpp
    function test_callInternalAdditionInParentFromChild() public {

        uint a = 10; uint b = 20;
        uint c = child.callInternalAdditionInParentFromChild(a , b);

        // test passes if the value of (a + b) is returned correctly 
        assertEq(c, 30);
    }

    function test_callPublicAdditionInParentFromChild() public {

        uint a = 10; uint b = 20;
        uint c = child.callPublicAdditionInParentFromChild(a , b);

        // test passes if the value of (a + b) is returned correctly 
        assertEq(c, 30);
    }
```

Finally, let us see if we can call the internal string defined in the parent contract, from within the child contract:

```cpp
    function test_callInternalStringInParentFromChild() public {

        string memory str = child.callInternalStringInParentFromChild();

        // test passes if the value of internalString is returned correctly 
        assertEq(str, "Internal String");
    }
```

To run the test file I need to run this command in my terminal:

```bash
forge test --match-path src/SolidityBasics/Visibility/Visibility.t.sol
```

Please note that the exact command will vary depending on your directory structure.
