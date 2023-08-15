## Getting Started


To follow along, I highly recommend that you install [Foundry](https://github.com/foundry-rs/foundry) on your system.
The exact installation instructions might vary depending on your system, but the [Foundry book](https://book.getfoundry.sh/ "Foundry book") is quite comprehensive.

Also, feel free to use [Remix](https://remix.ethereum.org/) for an interactive UI to interact with your smart contracts, although I'll be using Foundry throughout.
This will still be a useful resource for you if you decide to stick with Remix, which is more beginner friendly.

Once you have all the CLI tools part of Foundry installed in your system, you can get started by creating a new directory and running:

```sh
$  forge init
```

> 📝  **Note:**
> Foundry is a modular toolchain that consists currently of 4 different CLI tools. We will primarily be using Forge throughout, but
> you can read more about each of these tools in the [Foundry book](https://book.getfoundry.sh/).

You will notice a bunch of new files and directories. Here are the ones you primarily need to worry about for now:

1. **lib**: This directory is where all your dependencies are stored. You will find yourself relying on Solidity code written by others much too often. This directory is where all of that will be stored.

2. **src**: The src directory is where you typically store all your smart contract code.

3. **test**: The test directory is where you typically store all your test files.

4. **script**: This is where you typically write scripts that deploy new smart contracts to the blockchain, and interact with existing ones.

5. **foundry.toml**: We can use the toml file to customise virtually all aspects of Foundry's behaviour.

For now, to keep things simple, we will only be working with the src directory. All our code and tests will go inside this directory.

To test if your Foundry project was properly initialized, run:

```sh
$  forge build
```
This command compiles all the Solidity files within the parent directory, not just the src directory. For now, Forge will simply compile the Solidity code that ships along with the default initialization of a Foundry project.
