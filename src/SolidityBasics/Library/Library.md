# Library

Initialized using the `library` keyword, libraries in Solidity are usually a collection of view and pure functions that are expected to be used by multiple contracts.
A library is not 'inherited' as you would inherit a contract. A library is natively accessible to all contracts in the same file, and can be imported into other files as well.
Using a library in these cases abstracts away commonly used code and helps in building a more modular codebase.
Libraries are mainly distinguished by the visibility of the functions defined within them.
A library can have `internal`, `external` or `public`, functions but not `private` functions.

A library with only internal functions is deployed alongside the contract that uses it, and the code of the library is included in the bytecode of the contract.
On the other hand if your library has external or public functions, it is deployed separately and your contract has to be linked to that deployment manually to perform DELEGATECALL(s) to it.

## Example

This is how you would define a library in Solidity:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

// Define a library named MathLib
library MathLib {

    function cubeRoot() internal pure returns (uint256) {
        // ...
    }
}

// Define a contract that uses the library
// Note that the library is not inherited

contract Calculator {

    function calculateCubeRoot(uint256 _num) public pure returns (uint256) {
        // Call the cubeRoot function defined in the library
        return MathLib.cubeRoot(_num);
    }
}
```

In this tutorial, we will:

1. Take a look at what `CREATE3` is and how it works.
2. We will look at an implementation of the concept using the famous [solady](https://github.com/Vectorized/solady) repo.
3. Test and deploy a `CREATE3` factory contract on multiple chains to the same address.
4. Use these factories to deploy two different contracts on two chains to the same address.

