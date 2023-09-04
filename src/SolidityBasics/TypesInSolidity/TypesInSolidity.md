# Types in Solidity

Solidity is a statically typed language, which means that the type of every variable needs to be defined at compile time.
Solidity has many different types for us to use.
They can be broadly classified into two categories:

## 1. Value Types

Variables of value types store their data in a piece of memory they own.
These variables are passed by value when assigned to new variables or passed as function arguments.
Changing the value of the second variable won't alter the value of the original.
Solidity consists of the following value types:

- Booleans (*`bool`*)
- Unsigned integers (*`uint`*)
- Signed integers (*`int`*)
- Addresses (*`address`*)
- Enums (*`enum`*)
- Bytes (*`bytes`*)

## 2. Reference Types

Variables of reference types store a reference to the data in a piece of memory they don't own.
That piece of memory could be used by other reference type variables as well.
These variables are passed by reference when assigned to new variables or passed as function arguments.
A change in the value of the second variable can alter the value of the original.
Solidity consists of the following reference types:

- Arrays (*`[]`*)
- Structs (*`struct`*)
- Mappings (*`mapping`*)

Consider this bit of Solidity code:

```cpp
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract ValueType_vs_ReferenceType {

    //Value Type
    uint public valueVar1 = 10;
    
    // Reference Type: array of unsigned integers
    uint[] public referenceVar1 = [1, 2, 3];

    function modifyValueType(uint newValue) public view {

        // New Variable assigned the value of valueVar1
        uint valueVar2 = valueVar1;  

        //New variable passed 
        valueVar2 = newValue;
    }

    function modifyReferenceType(uint index, uint newValue) public {
        
        // New variable, referenceVar2, refers to the same storage location as referenceVar1
        uint[] storage referenceVar2 = referenceVar1;  
        
        // Modifying the localReference will modify referenceVar
        referenceVar2[index] = newValue;              
    }

}
```

This contract consists of two state variables, and two public functions:

1. `modifyValueType()` creates a new value type in memory, and assigns it an initial value from the original value type, before finally assigning it the value passed as the function argument.
This won't change the value of `valueVar1`, since only a temporary copy of the variable was used inside the function.

2. `modifyReferenceType()` creates a new reference type in memory that points to the same storage location as `referenceVar1`. Any change of value in `referenceVar2` will also be reflected in `referenceVar1`.

We can write a small test contract for the snippet as follows:

```cpp
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import {Test, console2} from "forge-std/Test.sol";
import "./Types.sol";

contract Types_test is Test {

    ValueType_vs_ReferenceType types;

    function setUp() public {
        types = new ValueType_vs_ReferenceType();
    }

    function test_modifyValueType() public {
        types.modifyValueType(1234);

        // Check that the value of valueVar1 is still 10
        assertEq(types.valueVar1(), 10);
    }

    function test_modifyReferenceType() public {
        console2.log("The original value of referenceVar1[0] is", types.referenceVar1(0));
        types.modifyReferenceType(0, 1234);

        // Check that the value of referenceVar1[0] is now 1234
        assertEq(types.referenceVar1(0), 1234);
    }
}
```

> 📝  **Note:**
> The [`Console2`](https://github.com/foundry-rs/forge-std/blob/74cfb77e308dd188d2f58864aaf44963ae6b88b1/src/console2.sol) library is used to log values to the console. It is imported from the `forge-std` package, just like the main `Test` 
> library.


