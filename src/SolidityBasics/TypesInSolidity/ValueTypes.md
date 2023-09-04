# Value Types

## Boolean
A `bool` variable can have two values: `true` or `false`.
Solidity supports the following operations on booleans:

- `==` (equality)
- `!=` (inequality)
- `!` (logical negation)
- `||` (logical disjunction, “OR”)
- `&&` (logical conjunction, “AND”)

## Integers
Solidity supports signed and unsigned integers of various sizes. They are represented using the `int` and `uint` keywords respectively, followed by the number of bits they occupy.
For example, `int256` is a signed integer occupying 256 bits, and `uint8` is an unsigned integer occupying 8 bits.

Solidity supports integers of sizes 8 bits to 256 bits, in steps of 8.
Integers can be initialized as `int` or `uint` without specifying the number of bits they occupy. In this case, they occupy 256 bits.

> 📝  **Note:**
> All integers in Solidity are limitied to a certain range. For example, `uint256` can store a value between 0 and 2<sup>256</sup>-1.
> Since `int256` is a signed integer, it can store a value between -2<sup>255</sup> and 2<sup>255</sup>-1.

## Addresses
An `address` variable stores a 20-byte/160-bits value (size of an Ethereum address).

> 📝  **Note:**
> EVM addresses are 40 characters long, however they are often represented as hexadecimal strings with a `0x` prefix.
> But strictly speaking, the address itself is 40 characters.

Solidity allows us to initialize a variable of type `address` in two ways:
- `address`: A simple 20-byte value that represents an EVM address. We can query the balance of an address variable using the `balance()` method.
- `address payable`: Any address variable initialzed with the `payable` keyword comes with two additional functions, `transfer()` and `send()`, that allow us to send ETH to the address.

Any integer can be typecasted into an address like this:

```cpp
address(1) == address(0x1) == 0x0000000000000000000000000000000000000001
```
In this case, the integer `1` will be treated as a `uint160`, which can be implicitly converted into an address type.

## Enums

Enums are a user-defined type that can have upto 256 members. They are declared using the `enum` keyword.
Each member of an enum corresponds to an integer value, starting from 0.
However, each member can be referenced directly by using its' explicit name.

Consider this example:

```cpp
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract TestEnum {
    // Define an enum named Directions
    enum Directions { Center, Up, Down, Left, Right }

    // Declare a state variable of type Directions with default value (Center, the first enum member)
    Directions public defaultDirection;

    // Declare and initialize another state variable
    Directions public setDirection = Directions.Right;

    // Change the direction
    function changeDirection(Directions newDirection) public {
        setDirection = newDirection;
    }

    // Get the maximum value of the Directions enum (i.e., Right in this case)
    function getMaxEnumValue() public pure returns (Directions) {
        return type(Directions).max;
    }
}
```

## Fixed-size byte arrays

The `bytes` type is used to store raw byte data. Even though bytes are always stored as an array of characters, fixed-size byte arrays are a value type, while dynamic-size byte arrays are reference type.

A fixed size byte array can be anywhere between 1 and 32 bytes in size. 
They are declared as:
 `bytes1`, `bytes2`, `bytes3`, .............. `bytes32`.

 Each byte can store 2 characters. Therefore, a `bytes20` variable can store upto 40 characters, enough for an Ethereum address.

> 📝  **Note:**
> All byte variables come with a `length` property that can be used to get the length of the bytes array.

Here is a code snippet that demonstrates the use of fixed-size byte arrays:

```cpp
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract ByteContract {

    // Declare a bytes20 variable to store data
    bytes20 public data;

    // Function to set data
    function setData(bytes20 _data) public {
        data = _data;
    }

    // Function to get the length of the bytes variable
    function getFirstByte() public view returns (bytes1) {
        return data[0];
    }

    function getLength() public view returns (uint){
        return data.length;
    }
 
}
```