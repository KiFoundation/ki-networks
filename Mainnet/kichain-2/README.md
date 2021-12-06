<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# Join the kichain-2

This guide will provide instructions to join the the current KiChain mainnet with Chain ID `kichain-2`.

## Veteran mode
1. Go vesion `1.16`
2. Ki-tools: https://github.com/KiFoundation/ki-tools.git tag `2.0.1`
3. Genesis: [`genesis.json`](https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Mainnet/kichain-2/genesis.json)
2. Seeds: [`seed.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/seeds.txt)
3. Persistent peers: [`peers.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/peers.txt)
4. Min gas price: `0.025uxki`

## Detailed steps
### Install GO
The current version of `ki-tools` requires Go `1.16`. To install it, download and unzip the archive file as follows:

```bash
wget https://dl.google.com/go/go1.16.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.16.linux-amd64.tar.gz
```

Then export the Go paths:
```bash
mkdir -p $HOME/go/bin
PATH=$PATH:/usr/local/go/bin
echo "export PATH=$PATH:$(go env GOPATH)/bin" >> ~/.bash_profile
source ~/.bash_profile
```

To test the Go installation, use the version command to check the downloaded version as follows :

```bash
go version
```
which should output

```bash
go version go1.16 linux/amd64
```

### Install Ki-tools
Fetch and install the current Mainnet `ki-tools` version.

```bash
git clone https://github.com/KiFoundation/ki-tools.git
cd ki-tools
git checkout -b v2.0.1 tags/2.0.1
make install
```

Check `ki-tools` version

```bash
kid version --long --log_level info
```

This should output

```bash
name: kitools
server_name: kid
version: Mainnet-IBC-v2.0.1-889c4a2ca6b228247f5cb9366c3c0c894592da27
commit: 889c4a2ca6b228247f5cb9366c3c0c894592da27
build_tags: netgo,ledger
go: go version go1.16 darwin/amd64
...

```

### Set up your node directory and start your relay

Create your node directory as follows:
```bash
export NODE_ROOT= <location of your choice>
mkdir -p $NODE_ROOT/kid $NODE_ROOT/kilogs
cd $NODE_ROOT
```

**Note:** The step above can be skipped. In this case the node will be created in the default location `/home/<user>/.kid/`

Initiate the needed configuration files using the `unsafe-reset-all` command:
```bash
kid unsafe-reset-all --home ./kid
```

Curl the genesis file to the `./kid/config/` folder:
```bash
curl https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Mainnet/kichain-2/genesis.json > ./kid/config/genesis.json
```

The hash of the genesis file can be computed as follows:
```bash
sha256sum ./kid/config/genesis.json

99855fdf89f5c697f8be2ecc587d79c77259e05d68268928797083bdaa614a80
```

In the file `config.toml` that can be found in the `./kid/config/` directory, make sure to add the provided seed and the community peers found in [`seed.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/seeds.txt) and [`peers.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/peers.txt) by filling the `seeds` and `persistent_peers` fields resp.

Make sure to set your `min-gas-price` to `0.025uxki` in `app.toml`.

Your node is now ready to be started. If you've started the node with a service you can simply start the service. Otherwise, use the following command:

```bash
kid start --home ./kid/ &> ./kilogs/ki-node.log &
```

This command will start the block synchronization process where your node retrieves the current state of the blockchain from the other nodes. The process output is redirected to the `ki-node.log` log file. It can be visualized as follows:

```bash
tail -f kilogs/ki-node.log
```

### Create your validator

**Note:** You will need to wait for the end of the synchronization process before proceeding to the validator creation.

Create the validator wallet. Do not forget to save the mnemonic.

```bash
kid keys add <WALLET_NAME> --home ./kid/
```

Fund the wallet with some XKIs. XKI Tokens can be bought on [Osmosis Dex](https://app.osmosis.zone/pool/577) or on [Liquid CEX](https://app.liquid.com/exchange/XKIUSDC).

Create your validator using the following transaction:

```bash
kid tx staking create-validator \
  --commission-max-change-rate=0.1 \
  --commission-max-rate=0.1 \
  --commission-rate=0.1 \
  --min-self-delegation=1 \
  --amount=1000000utki \
  --pubkey `kid tendermint show-validator --home ./kid/` \
  --moniker=<YOUR_MONIKER> \
  --chain-id=kichain-2 \
  --gas-prices="0.025uxki" \
  --from=<WALLET_NAME> \
  --home ./kid/  
```

To make your validator more identifiable and to attach additional information to it (e.g., a website), you can use an editing transaction as follow :

```bash
kid tx staking edit-validator  \
  --identity=<YOUR_KEYBASE_ID> \
  --website="<YOUR_WEBSITE>" \
  --details="<YOUR_DESCRIPTION>"\  
  --chain-id=kichain-2 \
  --from=<WALLET_NAME> \
  --gas-prices=0.025uxki \
  --home ./kid/
```

This will refer to an existing [Keybase](https://keybase.io) account and use its associated information (e.g. avatar image) as meta data for you validator.
