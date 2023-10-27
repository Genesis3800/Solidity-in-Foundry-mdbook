# Library

Initialized using the `library` keyword, libraries in Solidity are usually a collection of view and pure functions that are expected to be used by multiple contracts.
Using a library in these cases abstracts away commonly used code and helps in building a more modular codebase.

In this tutorial, we will:

1. Write a small library that performs a few basic arithmetic operations.
2. Write a few simple fuzz tests to understand why our library isn't very useful.
3. Understand the concepts of overflow and underflow in Solidity.
4. Look at [Openzeppelin's SafeMath library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/Math.sol) to see how it implements arithmetic operations.
5. Finally we will fuzz this library to see how it handles the edge cases that our library failed.