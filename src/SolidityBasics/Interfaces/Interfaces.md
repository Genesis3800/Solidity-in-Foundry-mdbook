# Interface

Interfaces are a collection of function declarations with no definitions.
An interface may not inherit from other contracts, may not define constructors, and may not define variables or modifiers.
They can however inherit from other interfaces.

Interfaces are useful to make external calls to other contracts already deployed on the blockchain.

In this tutorial, we will:

1. Write a simple smart contract with two functions.
2. Write an interface for the contract.
3. Write a contract that uses the interface to functions on the first contract.
4. Write a couple of test functions in Foundry to test the whole setup.

>📝  **Note:**
>All function declarations in an interface must be marked external, even if the underlying variables or functions being called are public.
