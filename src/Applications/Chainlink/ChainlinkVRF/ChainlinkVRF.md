# Chainlink VRF

[Chainlink VRF](https://docs.chain.link/docs/chainlink-vrf/) is a Chainlink service that allows smart contracts to consume provably fair and verifiable random numbers.
Blockchains by their nature are deterministic in nature, and therefore generating truly random numbers is nigh impossible.

In this tutorial, we will write a simple smart contract that consumes Chainlink's VRF service to return one of these 3 random numbers:
1, 2, or 3.

- We should get back ***1*** with a 1% probability.
- We should get back ***2*** with a 33% probability.
- We should get back ***3*** with a 66% probability.

Briefly, here is how Chainlink's VRF service works:

1. Your smart contract performs an external call on the VRF coordinator contract, by calling the `requestRandomWords()` function.
2. This emits an event that is picked up by Chainlink's off-chain node network.
3. The off-chain node network generates a random number and sends it back to the coordinator contract.
4. The coordinator contract sends the generated random number(s) to your smart contract by calling the `fulfillRandomWords()` function on your smart contract, which you must implement. This serves as the callback function.