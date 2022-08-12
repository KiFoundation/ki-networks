<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# Join the kichain-t-4

This guide will provide instructions to join the the current KiChain testnet with Chain ID `kichain-t-4`.

## Veteran mode
1. Go vesion `1.18`
1. Ki-tools: https://github.com/KiFoundation/ki-tools.git tag `3.0.0-beta`
1. Genesis: [`genesis.json`](https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Testnet/kichain-t-4/genesis.json)
1. Persistent peers: [`peers.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Testnet/kichain-t-4/peers.txt)
1. Min gas price: `0.025utki`

## Detailed steps
### Install GO
The current version of `ki-tools` requires Go `1.16`. To install it, download and unzip the archive file as follows:

```bash
wget https://dl.google.com/go/go1.18.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.18.linux-amd64.tar.gz
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
Fetch and install the current Testnet `ki-tools` version.

```bash
git clone https://github.com/KiFoundation/ki-tools.git
cd ki-tools
git checkout -b v3.0.0-beta tags/3.0.0-beta
make build-testnet
cp build/kid $HOME/go/bin/
```

Check `ki-tools` version

```bash
kid version --long --log_level info
```

This should output

```bash
name: kitools
server_name: kid
version: HEAD-59cae5f6c094fffb4bb483f7adbcd9838ceab852-Testnet-IBC
commit: 59cae5f6c094fffb4bb483f7adbcd9838ceab852
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
kid tendermint unsafe-reset-all --home ~/.kid
```

Curl the genesis file to the `~/.kid/config/` folder:
```bash
curl https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Testnet/kichain-t-4/genesis.json > ~/.kid/config/genesis.json
```

The hash of the genesis file can be computed as follows:
```bash
jq -S -c -M '' ~/.kid/config/genesis.json | shasum -a 256

83e8d8b9b883f1d478fb8d9f0e32c138904d0cbc30e939fa0490f988fd0b78d7
```

In the file `config.toml` that can be found in the `~/.kid/config/` directory, make sure to add the provided seed and the community peers found in [`seed.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Testnet/kichain-t-4//seeds.txt) and [`peers.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Testnet/kichain-t-4//peers.txt) by filling the `seeds` and `persistent_peers` fields resp.

Make sure to set your `minimum-gas-prices` to `0.025utki` in `app.toml`.

To get started, you might need to sync the data. To do so, you can ask a fellow validator for a snapshot containing data+wasm folders. 
You can also sync from the chain start. For this you will need to build and run kid `2.0.0-testnet` version, up until the block `4351400`, and then switch to the `3.0.0-beta` version. 
More information on the v3 upgrade [here](https://github.com/KiFoundation/ki-networks/blob/v0.1/Testnet/kichain-t-4/UPGRADE_V3.md) 

Your node is now ready to be started. If you've started the node with a service you can simply start the service. Otherwise, use the following command:

```bash
kid start --home ~/.kid/ &> ~/.kilogs/ki-node.log &
```

This command will start the block synchronization process where your node retrieves the current state of the blockchain from the other nodes. The process output is redirected to the `ki-node.log` log file. It can be visualized as follows:

```bash
tail -f ~/.kilogs/ki-node.log
```


### Create your validator

**Note:** You will need to wait for the end of the synchronization process before proceeding to the validator creation.

Create the validator wallet. Do not forget to save the mnemonic.

```bash
kid keys add <WALLET_NAME> --home ~/.kid/
```

Fund the wallet by asking for test token `TKIs` in the dedicated discord [channel](https://discord.gg/Baqd4ZZrhG).

Create your validator using the following transaction:

```bash
kid tx staking create-validator \
  --commission-max-change-rate=0.1 \
  --commission-max-rate=0.1 \
  --commission-rate=0.1 \
  --min-self-delegation=1 \
  --amount=1000000utki \
  --pubkey `kid tendermint show-validator --home ~/.kid/` \
  --moniker=<YOUR_MONIKER> \
  --chain-id=kichain-t-4 \
  --gas-prices="0.025utki" \
  --from=<WALLET_NAME>
```

The <YOUR_MONIKER> is the name you want to give to your validator.  
/!\ It's _not_ the same as the `moniker` parameter in the `config.toml` configuration file. Even if they have the same name, they do not represent the same thing.

### OPTIONAL: make your validator identifiable on Keybase

To make your validator more identifiable and to attach additional information to it (e.g., a website), you can use an editing transaction as follow :

```bash
kid tx staking edit-validator  \
  --identity=<YOUR_KEYBASE_ID> \
  --website="<YOUR_WEBSITE>" \
  --details="<YOUR_DESCRIPTION>"\  
  --chain-id=kichain-t-4 \
  --from=<WALLET_NAME> \
  --gas-prices=0.025utki
```

This will refer to an existing [Keybase](https://keybase.io) account and use its associated information (e.g. avatar image) as meta data for you validator.

### Validate your new validator

Go to [the list of the kichain testnet validators](https://kichain-t-4.blockchain.ki/validators) and when you see your validator with the <YOUR_MONIKER> name in the list, you are all good.
