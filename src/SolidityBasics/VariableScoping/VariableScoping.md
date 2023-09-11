# Local, state and global variables

Based on their scope, Solidity variables can be classified into:

1. **Local variables**:
These variables are declared inside a function and are only accessible inside that function. They are not stored on the blockchain.

2. **State variables**:
These variables are declared outside all functions but inside a contract. They can be accessed by all functions inside the contract. They are stored in the contract's storage, i.e. on the blockchain.

3. **Global variables**:
These variables exist in the global namespace and are part of the Solidity language. They are used to access blockchain or transaction data in real-time. The exact value of variables like `block.number` may depend on the node you are using to connect to the blockchain.
A complete reference to all global variables provided by Solidity can be found [on their documentation](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#block-and-transaction-properties).

Take a look at this code example for a clearer picture:

```cpp
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract VariableScopesInSolidity {

    // State variables are declared outside all functions
    uint public stateVariable = 123;

    function declareLocalVariable() public view {

        // Local variables are declared within a function
        uint localVariable = 123;        
    }

    function callAllVariables() public {

        // Local variable cannot be incremented
        // Compiler will throw an error
        localVariable += 1;

        // State variable can be incremented
        stateVariable += 1;

        // Global variables contain different types of blockchain data
        // Support for individual global variables may vary across blockchains 
        address sender = msg.sender; 
        uint Value = msg.value;
    }

}

```

> 📝  **Note:**
> Global variables basically read data in real-time. Different blockchains may implement the EVM in a different way.
> Therefore, the exact value of global variables may vary. In some cases the same global variable may return a totally different across different blockchains.
> For example, you can check out [Scroll Network's documentation](https://docs.scroll.io/en/developers/ethereum-and-scroll-differences/) on the differences in their
> Opcode support.
