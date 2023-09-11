# Visibility

Solidity provides four different types of visibility specifiers to help us control access to functions and variables.

Functions can be marked as `public`, `private`, `internal`, or `external`.
<br />
Variables can be marked as `public`, `private`, or `internal`.

1. **`public`**: Can be called by **any** contract or off-chain entity.
2. **`private`**: Can  only be called from **within** the main contract.
3. **`internal`**: Can be called from within the main contract **or** a child contract.
4. **`external`**: Can be called from other contracts that **do not** inherit from the main contract, or any other off-chain entities.
