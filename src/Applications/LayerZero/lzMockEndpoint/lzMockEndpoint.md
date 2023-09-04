# Testing LayerZero contracts using a mock endpoint

In this tutorial, we will:

1. Implement a simple example of cross-chain communication using two smart contracts.
2. Test out the contracts using `LZEndpointMock.sol`.

## What are we building?

We will implement two smart contracts, `OmniCounter_Mumbai` and `OmniCounter_Sepolia`, to be deployed on Mumbai and Sepolia respectively.

Both of them have a state variable named `counter` declared, which is initialized to 0 by default.
We want to leverage the LayerZero protocol so that when the `incrementCounter` function is called on either of the two contracts, the `counter` variable on the other contract is incremented by 1.

This will require a cross-chain message to be sent using LayerZero, and we will check if our contracts are correctly configured to receive and process these messages using the `LZEndpointMock.sol` contract.
