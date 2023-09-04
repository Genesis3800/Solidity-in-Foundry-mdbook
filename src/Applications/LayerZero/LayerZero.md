# Layer Zero

LayerZero is a cross-chain communication protocol that allows you to send messages from one chain to another through a decentralized network of relayers.
Popular decentralized exchanges like [Sushiswap](https://www.sushi.com/swap) and [PancakeSwap](https://bridge.pancakeswap.finance/) are already using LayzerZero to power their cross-chain swapping functionalities.

This section will gradually expand with more examples that go over LayerZero's features.

## How does LayerZero work?

The LayerZero protocol relies on a relayer + oracle combination to send messages from one chain to another.

1. Each supported network, both EVM and non-EVM have an '*endpoint*' deployed on it. This endpoint is essentially an entrypoint contract that serves as a point of assembly for all incoming and outgoing messages on the chain.
2. An off-chain relayer tracks the activity on these endpoints, and forwards the messages to the destination chain.
3. It is possible [to run your own relayer](https://layerzero.gitbook.io/docs/ecosystem/relayer/develop-a-relayer) and configure your smart contract so that it only sends messages through your relayer.
4. A relayer however isn't the last authority. In practice, a relayer is hooked up with an oracle.
A message is delivered to the destination chain only if the relayer and the oracle both agree on its' validity.
You can read more about this in their [whitepaper](https://layerzero.network/pdf/LayerZero_Whitepaper_Release.pdf).
5. It is possible to choose the specific oracle you want your relayer to work with. Read more about this [on their docs](https://layerzero.gitbook.io/docs/ecosystem/oracle).

## Sending cross-chain messages with LayerZero

Before moving on with any of the LayerZero tutorials, let us understand how an actual smart contract could leverage the protocol to send cross-chain messages:

1. Each network supported by the protocol has an endpoint deployed on it. For example, the Ethereum mainnet endpoint is deployed [on this address](https://etherscan.io/address/0x66A71Dcef29A0fFBDBE3c6a460a3B5BC225Cd675#code).
2. Any smart contract that wants to start a cross-chain message originating from the Ethereum mainnet, would need to call the `send()` function on the endpoint contract, with the message included as part of the calldata.
3. On the contrary, if this smart contract is to be made capable of receiving messages, it needs to implement the `_nonblockingLzReceive` function. This function serves as a callback function that is called by the endpoint contract when it receives a message that needs to be delivered to this contract.