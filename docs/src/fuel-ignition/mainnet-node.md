# Running a local Fuel node connected to Mainnet using P2P

> Fuel is preparing for the next major client release, upgrading the network from version 0.40.x to 0.41.x. This will be a required upgrade for all node operators.
> The **mainnet upgrade is scheduled for March 6**. You can upgrade immediately to the [0.41.6](https://github.com/FuelLabs/fuel-core/releases/tag/v0.41.6) release and sync with the current network using the latest release.
> This release brings database optimizations for some API queries. To take full advantage of these improvements, we recommend re-syncing the chain from the genesis block. While not mandatory, doing so will ensure the best performance.

## Installation

To install the Fuel toolchain, you can use the `fuelup-init` script.
This will install `forc`, `forc-client`, `forc-fmt`, `forc-lsp`, `forc-wallet` as well as `fuel-core` in `~/.fuelup/bin`.

```sh
curl https://install.fuel.network | sh
```

> Having problems? Visit the [installation guide](https://docs.fuel.network/guides/installation/) or post your question in our [forum](https://forum.fuel.network/).

## Getting a mainnet Ethereum API Key

An API key from any RPC provider that supports the Sepolia network will work. Relayers will help listen to events from the Ethereum network. We recommend either [Infura](https://www.infura.io/) or [Alchemy](https://www.alchemy.com/)

The endpoints should look like the following:

### Infura

```sh
https://mainnet.infura.io/v3/{YOUR_API_KEY}
```

### Alchemy

```sh
https://eth-mainnet.g.alchemy.com/v2/{YOUR_API_KEY}
```

Note that using other network endpoints will result in the relayer failing to start.

## Generating a P2P Key

Generate a new P2P key pairing by running the following command:

```sh
fuel-core-keygen new --key-type peering
{
  "peer_id":"16Uiu2HAmEtVt2nZjzpXcAH7dkPcFDiL3z7haj6x78Tj659Ri8nrs",
  "secret":"b0ab3227974e06d236d265bd1077bb0522d38ead16c4326a5dff2f30edf88496",
  "type":"peering"
}
### Do not share or lose this private key! Press any key to complete. ###
```

Make sure you save this somewhere safe so you don't need to generate a new key pair in the future.

## Chain Configuration

To run a local node with persistence, you must have a folder with the following chain configuration files:

For simplicity, clone the [repository](https://github.com/FuelLabs/chain-configuration/tree/master) into the directory of your choice.

When using the `--snapshot` flag later, you can replace `./your/path/to/chain_config_folder` with the `ignition` folder of the repository you just cloned `./chain-configuration/ignition/`.

## Running a Local Node

First ensure your environments [open files limit](https://askubuntu.com/questions/162229/how-do-i-increase-the-open-files-limit-for-a-non-root-user) `ulimit` is increased, example:

```sh
ulimit -S -n 32768
```

> Please make sure you have the [latest version](https://docs.fuel.network/guides/installation/#updating-fuelup) of the Fuel toolchain installed and properly configured before continuing.

Finally to put everything together to start the node, run the following command:

```sh
fuel-core run \
--enable-relayer \
--service-name fuel-mainnet-node \
--keypair {P2P_PRIVATE_KEY} \
--relayer {ETHEREUM_RPC_ENDPOINT} \
--ip=0.0.0.0 --port 4000 --peering-port 30333 \
--db-path ~/.fuel-mainnet \
--snapshot ./your/path/to/chain_config_folder \
--utxo-validation --poa-instant false --enable-p2p \
--bootstrap-nodes /dnsaddr/mainnet.fuel.network \
--sync-header-batch-size 50 \
--relayer-v2-listening-contracts=0xAEB0c00D0125A8a788956ade4f4F12Ead9f65DDf \
--relayer-da-deploy-height=20620434 \
--relayer-log-page-size=100 \
--sync-block-stream-buffer-size 30
```

For the full description details of each flag above, run:

```sh
fuel-core run --help
```

## Connecting to the local node from a browser wallet

To connect to the local node using a browser wallet, import the network address as:

```sh
http://0.0.0.0:4000/v1/graphql
```
