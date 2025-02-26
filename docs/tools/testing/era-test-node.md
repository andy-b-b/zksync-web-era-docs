# In-memory node

This documentation provides instructions on setting up and using the In-Memory Node, `era-test-node`, for local testing. It covers installation, network forking, transaction details viewing, replaying transactions, and testing local bootloader and system contracts.

:::warning Tool in alpha stage
 Please keep in mind that `era-test-node` is still in its **alpha** stage, so some features might not be fully supported yet and may not work as fully intended. It is [open-sourced](https://github.com/matter-labs/era-test-node) and contributions are welcomed. 
:::

## Understanding the In-Memory Node 

The In-memory node uses an in-memory database for storing state information and simplified hashmaps for tracking blocks and transactions. In fork mode, it retrieves missing storage data from a remote source when not available locally. Moreover it also uses the remote server (openchain) to resolve the ABI and topics to human readable names.

You can visit the `era-test-node` repository [here](https://github.com/matter-labs/era-test-node) to learn more.

## Prerequisites 

Before you get started, there are a few prerequisites to consider. Ensure that your development environment meets the following requirements:

- **Rust:** Since `era-test-node` is written in Rust, you need to have Rust installed on your machine. You can download Rust from [here](https://www.rust-lang.org/tools/install).
- **Other Dependencies:** This crate relies on `rocksDB` for its operation. If you encounter any compile errors due to `rocksDB`, you might also need to install some dependencies with the following command: `apt-get install -y cmake pkg-config libssl-dev clang`.

After checking all these prerequisites, you should be ready to use the `era-test-node`. Please keep in mind that `era-test-node` is still in its **alpha** stage, so some features might not be fully supported yet.

## Installing and setting up `era-test-node`

Begin by installing `era-test-node` using the command:

```bash
cargo install --git https://github.com/matter-labs/era-test-node.git
```

Rust should install it in the `~/.cargo/bin` directory.

To start the node, execute:

```bash
era_test_node run
```

The expected output will be as follows:

```bash
Starting network with chain id: L2ChainId(270)
Setting Rich accounts:
Address: "0x36615Cf349d7F6344891B1e7CA7C72883F5dc049" Key: "0x7726827caac94a7f9e1b160f7ea819f172f7b6f9d2a97f992c38edeab82d4110"
Address: "0xa61464658AfeAf65CccaaFD3a512b69A83B77618" Key: "0xac1e735be8536c6534bb4f17f06f6afc73b2b5ba84ac2cfb12f7461b20c0bbe3"
Address: "0x0D43eB5B8a47bA8900d84AA36656c92024e9772e" Key: "0xd293c684d884d56f8d6abd64fc76757d3664904e309a0645baf8522ab6366d9e"
Address: "0xA13c10C0D5bd6f79041B9835c63f91de35A15883" Key: "0x850683b40d4a740aa6e745f889a6fdc8327be76e122f5aba645a5b02d0248db8"
========================================
  Node is ready at 127.0.0.1:8011
========================================
```

:::warning
When utilizing `era-test-node` with MetaMask, it's essential to note that any restart of the in-memory node will necessitate a reset of MetaMask's cached account data (nonce, etc). To do this, navigate to 'Settings', then 'Advanced', and finally, select 'Clear activity tab data'.
:::

### Network details

The `era_test_node` has the following default network configurations: 

- **L2 RPC:** `http://localhost:8011`
- **Network Id:** 260

These can be configured to your preference. 

::: warning
Please note that the existing implementation does not facilitate communication with Layer 1. As a result, an L1 RPC is not available. 
:::

## Forking networks 

To fork the mainnet, use the following command:

```bash
# era_test_node fork <NETWORK>
era_test_node fork mainnet
```

:::tip
You can also fork testnet with `era_test_node fork testnet`
:::

The expected output will be as follows:

```bash
Creating fork from "https://testnet.era.zksync.dev:443" L1 block: L1BatchNumber(119993) L2 block: 9602073 with timestamp 1689261421 and L1 gas price 89159417930
Starting network with chain id: L2ChainId(260)

Rich Accounts
=============
Account #0: 0x36615Cf349d7F6344891B1e7CA7C72883F5dc049 (10000 ETH)
Private Key: 0x7726827caac94a7f9e1b160f7ea819f172f7b6f9d2a97f992c38edeab82d4110

Account #1: 0xa61464658AfeAf65CccaaFD3a512b69A83B77618 (10000 ETH)
Private Key: 0xac1e735be8536c6534bb4f17f06f6afc73b2b5ba84ac2cfb12f7461b20c0bbe3

Account #2: 0x0D43eB5B8a47bA8900d84AA36656c92024e9772e (10000 ETH)
Private Key: 0xd293c684d884d56f8d6abd64fc76757d3664904e309a0645baf8522ab6366d9e

Account #3: 0xA13c10C0D5bd6f79041B9835c63f91de35A15883 (10000 ETH)
Private Key: 0x850683b40d4a740aa6e745f889a6fdc8327be76e122f5aba645a5b02d0248db8

========================================
  Node is ready at 127.0.0.1:8011
========================================
```

This command starts the node, forked at the current head of the mainnet.

You also have the option to specify a custom http endpoint and a custom forking height, like so:

```bash
# Usage: era_test_node fork --fork-at <FORK_AT> <NETWORK
era_test_node fork --fork-at 7000000 mainnet http://172.17.0.3:3060
```

## Replay remote transactions locally 

If you wish to replay a remote transaction locally for deep debugging, use the following command:

```bash
# Usage: era_test_node replay_tx <NETWORK> <TX>
era_test_node replay_tx testnet 0x7f039bcbb1490b855be37e74cf2400503ad57f51c84856362f99b0cbf1ef478a
```

For more detailed transaction information, such as call traces, add the `--show-calls` flag. If you want to see ABI names, add the `--resolve-hashes` flag. Here's an example:

```bash
# Usage: era_test_node replay_tx <NETWORK> <TX>
era_test_node --show-calls=user --resolve-hashes replay_tx testnet 0x7f039bcbb1490b855be37e74cf2400503ad57f51c84856362f99b0cbf1ef478a
```

Here's an example of what you should expect to see:

```bash
Creating fork from "https://testnet.era.zksync.dev:443" L1 block: L1BatchNumber(94420) L2 block: 8072359 with timestamp 1687337488 and L1 gas price 2500011172
Starting network with chain id: L2ChainId(280)
Running 1 transactions (one per batch)

Executing 0x7f039bcbb1490b855be37e74cf2400503ad57f51c84856362f99b0cbf1ef478a
Transaction: SUCCESS
Initiator: 0x55362182242a4de20ea8a0ec055b2134bb24e23d Payer: 0x55362182242a4de20ea8a0ec055b2134bb24e23d
Gas Limit: 797128 used: 351250 refunded: 445878

==== Console logs:

==== 18 call traces.  Use --show-calls flag to display more info.
Call(Normal) 0x55362182242a4de20ea8a0ec055b2134bb24e23d           validateTransaction(bytes32, bytes32, tuple)   730485
  Call(Normal) 0x0000000000000000000000000000000000000001                 0xbb1e83e6   698040
Call(Normal) 0x55362182242a4de20ea8a0ec055b2134bb24e23d           payForTransaction(bytes32, bytes32, tuple)   703647
Call(Normal) 0x55362182242a4de20ea8a0ec055b2134bb24e23d           executeTransaction(bytes32, bytes32, tuple)   647199
    Call(Mimic) 0x6eef3310e09df3aa819cc2aa364d4f3ad2e6ffe3           swapExactETHForTokens(uint256,address[],address,uint256)   596358
      Call(Normal) 0x053f26a020de152a947b8ba7d8974c85c5fc5b81           getPair(address,address)   577647
      Call(Normal) 0xc0b7c869ba924c05f64333d9caa21f4424eb4b30           getReserves()   570402
        Call(Mimic) 0x8a144308792a23aadb118286ac0dec646f638908           deposit()   534807
      Call(Normal) 0x053f26a020de152a947b8ba7d8974c85c5fc5b81           getPair(address,address)   526050
      Call(Normal) 0x8a144308792a23aadb118286ac0dec646f638908           transfer(address,uint256)   522900
      Call(Normal) 0x053f26a020de152a947b8ba7d8974c85c5fc5b81           getPair(address,address)   497007
      Call(Normal) 0xc0b7c869ba924c05f64333d9caa21f4424eb4b30           swap(uint256,uint256,address,bytes)   492660
        Call(Normal) 0x880f03ca84e6cf0d0871c9818a2981debaba22b3           transfer(address,uint256)   465948
        Call(Normal) 0x880f03ca84e6cf0d0871c9818a2981debaba22b3           balanceOf(address)   432495
        Call(Normal) 0x8a144308792a23aadb118286ac0dec646f638908           balanceOf(address)   430290

==== 9 events
EthToken System Contract                   Transfer(address,address,uint256), 0x0000…e23d, 0x0000…8001
EthToken System Contract                   Transfer(address,address,uint256), 0x0000…e23d, 0x0000…ffe3
EthToken System Contract                   Transfer(address,address,uint256), 0x0000…ffe3, 0x0000…8908
0x8a144308792a23aadb118286ac0dec646f638908 Deposit(address,uint256), 0x0000…ffe3
0x8a144308792a23aadb118286ac0dec646f638908 Transfer(address,address,uint256), 0x0000…ffe3, 0x0000…4b30
0x880f03ca84e6cf0d0871c9818a2981debaba22b3 Transfer(address,address,uint256), 0x0000…4b30, 0x0000…e23d
0xc0b7c869ba924c05f64333d9caa21f4424eb4b30 Sync(uint112,uint112)
0xc0b7c869ba924c05f64333d9caa21f4424eb4b30 Swap(address,uint256,uint256,uint256,uint256,address), 0x0000…ffe3, 0x0000…e23d
EthToken System Contract                   Transfer(address,address,uint256), 0x0000…8001, 0x0000…e23d
```

## Sending network calls

You can send network calls against a running `era-test-node`. You can check the testnet LINK balance or mainnet USDT using `curl` or [foundry-zksync](https://github.com/matter-labs/foundry-zksync). 

To get started, launch the local in-memory node:

```bash
era_test_node fork testnet
```

Next, use curl to send a network call:

```bash
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_call","params":[{"to":"0x40609141Db628BeEE3BfAB8034Fc2D8278D0Cc78", "data":"0x06fdde03"}, "latest"],"id":1}' http://localhost:8011
```

Here's an example of what you should expect to see:

```bash
{"jsonrpc":"2.0","result":"0x00000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000018436861696e4c696e6b20546f6b656e2028676f65726c69290000000000000000","id":1}
```

Or, if you prefer, use [`foundry-zksync`](https://github.com/matter-labs/foundry-zksync). Make sure to install and configure `foundry-zksync` before proceeding (for installation instructions, please see this [link](https://github.com/matter-labs/foundry-zksync/tree/main#foundry-with-zksync-era-v01)):

```bash
zkcast call 0x40609141Db628BeEE3BfAB8034Fc2D8278D0Cc78 "name()(string)" --rpc-url http://localhost:8011
```

Here's an example of what you should expect to see:

```bash
ChainLink Token (goerli)
```

Retrieve the balance of a particular contract:

```bash
zkcast call 0x40609141Db628BeEE3BfAB8034Fc2D8278D0Cc78 "balanceOf(address)(uint256)"  0x40609141Db628BeEE3BfAB8034Fc2D8278D0Cc78  --rpc-url http://localhost:8011
```

Here's an example of what you should expect to see: 

```bash
28762283719941475444443116625665
```

### Deploying contracts

For the deployment of your contracts, you have the flexibility to choose between two preferred methods: either by using Hardhat with the `hardhat-zksync-deploy` and `hardhat-zksync-solc` plugins, or via `foundry-zksync`. The following example will detail the process using `foundry-zksync`.

Before proceeding, ensure that you've compiled your contracts using `zkforge zk-build`. For instructions on how to do this, please refer to this [link](https://github.com/matter-labs/foundry-zksync#compile-with-zkforge-zk-build).

```bash
zkforge zkc contracts/Greeter.sol:Greeter --constructor-args "ZkSync and Foundry" --private-key 7726827caac94a7f9e1b160f7ea819f172f7b6f9d2a97f992c38edeab82d4110 --rpc-url http://localhost:8011 --chain 270
```

Here's an example of what you should expect to see: 

```bash
Deploying contract...
+-------------------------------------------------+
Contract successfully deployed to address: 0x0a40ecde17dc16c4001bf0e4f5d5ff1818219b3b
Transaction Hash: 0x9d59bea38ca6f3cef365c23f339547bcc8ce28abb8344999ffffa5fa62c9ff8e
Gas used: 2570407
Effective gas price: 500
Block Number: 8072361
+-------------------------------------------------+
```

## Testing bootloader and system contracts

In-memory node allows testing of the currently compiled bootloader and system contracts. This makes it possible to examine the effects of changes on already deployed contracts.

:::warning
These commands assume you have set `$ZKSYNC_HOME` in your shell profile file (e.g. ~/.bash_profile, ~/.zshrc) to target your local copy of `era-test-node`. For instance, 

```bash
# Add path here:
export ZKSYNC_HOME=/path/to/era-test-node

export PATH=$ZKSYNC_HOME/bin:$PATH
```
:::

Firstly, you will need to preprocess and compile the contracts:

```bash
cd etc/system-contracts
yarn preprocess && yarn hardhat run ./scripts/compile-yul.ts
```

To use the locally compiled bootloader and system contracts, run:

```bash
RUST_LOG=vm=trace era_test_node --dev-use-local-contracts fork testnet
```

## Use pre-configured rich wallets

In-Memory node includes pre-configured "rich" accounts for testing:

| account id | private key |
|---|---|
| 0x36615Cf349d7F6344891B1e7CA7C72883F5dc049 | 0x7726827caac94a7f9e1b160f7ea819f172f7b6f9d2a97f992c38edeab82d4110 |
| 0xa61464658AfeAf65CccaaFD3a512b69A83B77618 | 0xac1e735be8536c6534bb4f17f06f6afc73b2b5ba84ac2cfb12f7461b20c0bbe3 | 
| 0x0D43eB5B8a47bA8900d84AA36656c92024e9772e | 0xd293c684d884d56f8d6abd64fc76757d3664904e309a0645baf8522ab6366d9e |
| 0xA13c10C0D5bd6f79041B9835c63f91de35A15883 | 0x850683b40d4a740aa6e745f889a6fdc8327be76e122f5aba645a5b02d0248db8 |


## Writing and running tests locally

This section demonstrates how to author and execute tests locally against `era-test-node` using the `mocha` and `chai` testing frameworks.

### Project configuration

1. Start by creating a new Hardhat project. If you need guidance, follow the [getting started guide](../../tools/hardhat/getting-started.md).

2. To incorporate the test libraries, execute:

```bash
yarn add -D mocha chai @types/mocha @types/chai
```

3. Add the following lines to your `package.json` in the root folder:

```json
"scripts": {
    "test": "NODE_ENV=test hardhat test"
}
```

This script makes it possible to run tests in a Hardhat environment with the `NODE_ENV` env variable set as `test`.

### Configuring tests

4. Adjust `hardhat.config.ts` to use the local node for testing:

:::warning
Ensure era-test-node is running in another process before executing yarn test. 
:::

```typescript
import "@matterlabs/hardhat-zksync-deploy";
import "@matterlabs/hardhat-zksync-solc";

// dynamically changes endpoints for local tests
const zkSyncTestnet =
  process.env.NODE_ENV == "test"
    ? {
        url: "http://localhost:8011",
        ethNetwork: "http://localhost:8545",
        zksync: true,
      }
    : {
        url: "https://testnet.era.zksync.dev",
        ethNetwork: "goerli",
        zksync: true,
      };

module.exports = {
  zksolc: {
    version: "latest", // Uses latest available in https://github.com/matter-labs/zksolc-bin/
    settings: {},
  },
  // defaults to zkSync network
  defaultNetwork: "zkSyncTestnet",
  networks: {
    hardhat: {
      zksync: true,
    },
    // load test network details
    zkSyncTestnet,
  },
  solidity: {
    version: "0.8.17",
  },
};
```

### Writing test scripts

5. Now, create your first test! Construct a `test/main.test.ts` file with the following code:

```ts
import { expect } from "chai";
import { Wallet, Provider, Contract } from "zksync-web3";
import * as hre from "hardhat";
import { Deployer } from "@matterlabs/hardhat-zksync-deploy";

const RICH_WALLET_PK = "<RICH_WALLET_PK>";

async function deployGreeter(deployer: Deployer): Promise<Contract> {
  const artifact = await deployer.loadArtifact("Greeter");
  return await deployer.deploy(artifact, ["Hi"]);
}

describe("Greeter", function () {
  it("Should return the new greeting once it's changed", async function () {
    const provider = Provider.getDefaultProvider();

    const wallet = new Wallet(RICH_WALLET_PK, provider);
    const deployer = new Deployer(hre, wallet);

    const greeter = await deployGreeter(deployer);

    expect(await greeter.greet()).to.eq("Hi");

    const setGreetingTx = await greeter.setGreeting("Hola, mundo!");
    // wait until the transaction is mined
    await setGreetingTx.wait();

    expect(await greeter.greet()).to.equal("Hola, mundo!");
  });
});
```

To run the test file, execute:

```bash
yarn test
```

Well done! You've successfully run your first local tests with zkSync Era and `era-test-node`.