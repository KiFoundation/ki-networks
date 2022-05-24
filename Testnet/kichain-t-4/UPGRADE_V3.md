<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# kichain-t-4 V3 Upgrade

The V3 upgrade `kichain-t-4` is set to happen at height `xxx` which at current block time should be reached on `xxx` between xxx  and xxx UTC.

## How to upgrade your node

Use the [provided](https://github.com/KiFoundation/ki-networks/tree/added-v3-upgrade-tutorial/Testnet/kichain-t-4) build or build the new client yourself. A full how-to can be found in this [dedicated tutorial](https://github.com/KiFoundation/ki-tools#readme).

We provide static binary releases with verifiable checksums. Never trust, always verify, so we encourage validators to build these binaries themselves using our dedicated tutorial and check the resulting checksum.

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
mkdir -p <NODE_HOME>/cosmovisor/upgrades/v3/bin
wget <URLRELEASETODO> && chmod +x kid
sha256sum kid # Should be TODOCHECKSUM
cp kid <NODE_HOME>/cosmovisor/upgrades/v3/bin
# this should return Testnet-3.0.0-beta.1
<NODE_HOME>/cosmovisor/upgrades/v3/bin/kid version
```

When the upgrade height is reached, Cosmovisor will automatically restart using the new binary.

### Without Cosmovisor
To upgrade your node using without using Cosmovisor,  you need to follow these steps:
1. Wait the chain halt. When its is reached, the node will stop. If the `kid` process is not killed by it self, kill it with the `kill` command.
2. Download the provided client or build it as shown earlier.
3. Start the node with the new client : `kid start --home <NODE_HOME>`

## Security

If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
