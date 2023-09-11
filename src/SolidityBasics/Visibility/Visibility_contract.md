# Visibility.sol

> 💻 The code corresponding to this page can be found on Github at [Visibility.sol](https://github.com/Genesis3800/Solidity-in-Foundry-Repo/blob/main/src/SolidityBasics/Visibility/Visibility.sol).

Let us define a few state variables and functions of different visibility types. We will then call a few of them from a contract that inherits from our main contract.

Define the main contract like this:

```cpp
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract Parent {

    // State variables cannot be marked external
    string public publicString = "Public String";
    string private privateString = "Private String";
    string internal internalString = "Internal String";

    /**
     * @dev Declaring 4 consecutive functions with different visibilities that do the same thing
     * @param (a , b) each function takes two variables as params and returns the sum.
     */
        
    function publicAddition(uint a , uint b) public pure returns (uint) {
        return a+b;
    }

    function privateAddition(uint a , uint b) private pure returns (uint) {
        return a+b;
    }

    function internalAddition(uint a , uint b) internal pure virtual returns (uint) {
        return a+b;
    }

    function externalAddition(uint a , uint b) external pure returns (uint) {
        return a+b;
    }


    /**
     * @dev We cannot call external functions from within the same contract.
            Note that since all the functions that are calling the functions
            above are marked public, the visibility specifiers don't do much 
            in our code. Not meant for production.            
    */

    function callPrivateAddition(uint a , uint b) public pure returns (uint) {
        return privateAddition(a , b);
    }

    function callPublicAddition(uint a , uint b) public pure virtual returns (uint) {
        return publicAddition(a , b);
    }

    function callInternalAddition(uint a , uint b) public pure virtual returns (uint) {
        return internalAddition(a , b);
    }

}
```

Now let us define a child contract that inherits from the main contract, and calls a few of the functions and variables defined in the main contract.

```cpp
contract Child is Parent {

    /**
     * @dev We cannot call private or external functions from inside a child contract.          
    */

    function  callInternalAdditionInParentFromChild(uint a , uint b) public pure returns (uint) {
        return internalAddition(a , b);
    }

    function callPublicAdditionInParentFromChild(uint a , uint b) public pure returns (uint) {
        return publicAddition(a , b);
    }

    function callInternalStringInParentFromChild() public view returns (string memory) {
        return internalString;
    }

}
```

> 📝  **Note:**
> Note that I haven't shown you how to call external functions of a contract from another contract. To understand how to do that, you 
> will need to understand how interfaces work in Solidity.
> We will learn how to call external functions in the interfaces section.
