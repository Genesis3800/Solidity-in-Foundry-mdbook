## HelloWorld.sol

The first line of almost all smart contracts starts something like this:

```cpp
// SPDX-License-Identifier: MIT
```

- The [SPDX license](https://spdx.dev/ "SPDX license") does not have anything to do with the code itself. The Solidity Compiler (solc) encourages the use of a license at the top of every Solidity file to clearly define the copyright legalities of the code.

- I will be using the MIT license throughout the book, one of the most permissive licenses out there. Any code written in this book is freely available to anyone and everyone for any use.
A full list of all SPDX licenses can be found [on their website](https://spdx.org/licenses/ "on their website").

Paste this below the License identifier:
```cpp
pragma solidity ^0.8.19;
```
- Every Solidity codebase must specify the versions of the [Solidity Compiler(solc)](https://www.npmjs.com/package/solc "Solidity Compiler(solc)") it is compatible with.
- Long story short, code written for older versions of the solc might not be compatible with the newer ones.
- It is important therefore, to make sure that the code you are writing is compiled with the correct version of the solc.

There are 3 main ways to specify the solc version:
 
1. `pragma solidity ^0.8.0;`
This will ensure that your contract is compiled with a solc version equal to or greater than 0.8.0, but less than 0.9.0.
The idea here is to that your contract won't be compiled with a solc version that will break your code.
The solc will introduce breaking changes only with major versions.
These versions have the form 0.x.0 or x.0.0.

2. `pragma solidity >=0.8.0 <0.8.14;`
This is a way to specify the exact range of the solc you want to use in your contract.

3. `pragma solidity 0.8.0`
This way you can make sure your contract only compiles with a specific compiler version.

Now we are ready to initialize a new smart contract using the `contract` keyword:
```cpp
contract HelloWorld { }
```
All the code you write goes within these curly brackets.

> 📝  **Note:**
> A single Solidity file may contain multiple smart contracts within it. 
> Pragma defines the Solidity version for the entire file, not a single contract.

Our contract consists of a single string that returns the string "Hello world".
Paste this within the curly brackets:

```cpp
string public greeting = "Hello World";
```

And that's it. You created a Hello World smart contract. Your code should look something like this:
```cpp
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.19;

contract HelloWorld {

    string public greeting = "Hello World";
}
```

If you are working with Remix, you can compile your contract by clicking `Ctrl + S`.

If you are working with Foundry, make sure you have a terminal open in the parent directory, the same directory where you initalized the project.
Then run the following command:

```sh
forge build
```

This command will compile all the Solidity files that exist within the parent directory.
If you've reached this far, and want to write a corresponding test for this contract, create a new file named `HelloWorld.t.sol` on the same level as this file.