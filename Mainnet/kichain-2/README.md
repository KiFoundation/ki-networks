<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# Join the kichain-2

This guide will provide instructions to join the the current KiChain mainnet with Chain ID `kichain-2`.

## Veteran mode

1. Go vesion `1.18`
2. Ki-tools: <https://github.com/KiFoundation/ki-tools.git> tag `3.0.0`
3. Genesis: [`genesis.json`](https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Mainnet/kichain-2/genesis.json)
2. Seeds: [`seed.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/seeds.txt)
3. Persistent peers: [`peers.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/peers.txt)
4. Min gas price: `0.025uxki`

## Detailed steps

### Install kid binary

Use the [provided](https://github.com/KiFoundation/ki-tools/releases/tag/3.0.0) builds or build the new client yourself. A full how-to guide can be found in this [dedicated tutorial](https://github.com/KiFoundation/ki-tools#readme).

We provide static binary releases with verifiable checksums. We encourage validators and node operators to build these binaries themselves using our dedicated tutorial and check the resulting checksum.

The client version should be:

```bash
kid version --long

# name: kitools
# server_name: kid
# version: Mainnet-3.0.0
# commit: dac9a74443d5cdc9d9a87214e03d77d3e2f7883d
# build_tags: netgo ledger muslc,
# go: go version go1.17.10 linux/amd64
```

### Initialize your node

Initialize the needed configuration files using the `unsafe-reset-all` command:

```bash
kid unsafe-reset-all --home ~/.kid
```

Curl the genesis file to the `~/.kid/config/` folder:

```bash
curl https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Mainnet/kichain-2/genesis.json > ~/.kid/config/genesis.json
```

The hash of the genesis file can be computed as follows:

```bash
jq -S -c -M '' ~/.kid/config/genesis.json | shasum -a 256

99855fdf89f5c697f8be2ecc587d79c77259e05d68268928797083bdaa614a80
```

In the file `config.toml` that can be found in the `~/.kid/config/` directory, make sure to add the provided seed and the community peers found in [`seed.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/seeds.txt) and [`peers.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/peers.txt) by filling the `seeds` and `persistent_peers` fields resp.

Make sure to set your `min-gas-price` to `0.025uxki` in `app.toml`.

To get started, you might need to sync the data. To do so, you can use a data snapshot.
You can also sync from the chain start. For this you will need to build and run kid `2.0.1` version, up until the block `10155750`, and then switch to the `3.0.0` version.
More information on the v3 upgrade [here](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/UPGRADE_V3.md)

Your node is now ready to be started. If you've started the node with a service you can simply start the service. Otherwise, use the following command:

```bash
kid start &> ~/.kilogs/ki-node.log &
```

This command will start the block synchronization process where your node retrieves the current state of the blockchain from the other nodes. The process output is redirected to the `ki-node.log` log file. It can be visualized as follows:

```bash
tail -f ~/.kilogs/ki-node.log
```

### Create your validator

**Note:** You will need to wait for the end of the synchronization process before proceeding to the validator creation.

Create the validator wallet. Do not forget to save the mnemonic.

```bash
kid keys add <WALLET_NAME> --home
```

Fund the wallet with some XKIs. XKI Tokens can be bought on [Osmosis Dex](https://app.osmosis.zone/pool/577).

Create your validator using the following transaction:

```bash
kid tx staking create-validator \
  --commission-max-change-rate=0.1 \
  --commission-max-rate=0.1 \
  --commission-rate=0.1 \
  --min-self-delegation=1 \
  --amount=1000000uxki \
  --pubkey `kid tendermint show-validator` \
  --moniker=<YOUR_MONIKER> \
  --chain-id=kichain-2 \
  --gas-prices="0.025uxki" \
  --from=<WALLET_NAME>
```

To make your validator more identifiable and to attach additional information to it (e.g., a website), you can use an editing transaction as follow :

```bash
kid tx staking edit-validator  \
  --identity=<YOUR_KEYBASE_ID> \
  --website="<YOUR_WEBSITE>" \
  --details="<YOUR_DESCRIPTION>"\  
  --chain-id=kichain-2 \
  --from=<WALLET_NAME> \
  --gas-prices=0.025uxki
```

This will refer to an existing [Keybase](https://keybase.io) account and use its associated information (e.g. avatar image) as meta data for you validator.
