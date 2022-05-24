<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# kichain-t-4 V3 Upgrade

The V3 upgrade `kichain-t-4` is set to happen at height `xxx` which at current block time should be reached on `xxx` between xxx  and xxx UTC.

## How to upgrade your node

Use the [provided]() build or build the new client yourself. A full tutorial can be found in this [dedicated tutorial](https://github.com/KiFoundation/ki-tools#readme).

The client version should be:
```bash
kid version --long

# name: kitools
# server_name: kid
# version: Testnet-3.0.0-alpha
# commit: 3ba8ff86c390a21dd401fcd3da53dc2aa9974170
# build_tags: netgo,ledger
# go: go version go1.17 linux/amd64
```

### With Cosmovisor
To upgrade your node using Cosmovisor you need to follow these steps:

```bash
mkdir -p <NODE_HOME>/cosmovisor/upgrades/v3/bin && cp $HOME/go/bin/junod <NODE_HOME>/cosmovisor/upgrades/v3/bin
# this will return v4.0.0
<NODE_HOME>/cosmovisor/upgrades/v3/bin/kid version
```

When the halt height is reached, Cosmovisor will automatically restart the chain with the new binary.

### Without Cosmovisor
To upgrade your node using without using Cosmovisor,  you need to follow these steps:
1. Wait the chain halt. When its is reached, the node will stop. If the process is not killed by it self, kill it with the `kill` command.
2. Download the provided client or build it as shown earlier.
3. Start the node with the new client : `kid start --home <NODE_HOME>`

## Security

If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
