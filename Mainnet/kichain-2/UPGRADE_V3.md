<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# kichain-t-4 V3 Upgrade

The V3 upgrade `kichain-2` is set to happen at height `10155750` which at current block time should be reached on `15th of June 2022` around `13H00 UTC`.

## How to upgrade your node

Use the [provided](https://github.com/KiFoundation/ki-tools/releases/tag/3.0.0) builds or build the new client yourself. A full how-to guide can be found in this [dedicated tutorial](https://github.com/KiFoundation/ki-tools#readme).

We provide static binary releases with verifiable checksums. We encourage validators to build these binaries themselves using our dedicated tutorial and check the resulting checksum.

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

### With Cosmovisor
To upgrade your node using Cosmovisor you need to follow these steps:

```bash
# Check that your $DAEMON_HOME is correctly set
echo $DAEMON_HOME

# Download new binary (amd64 version, please adapt if you use an arm arch)
wget https://github.com/KiFoundation/ki-tools/releases/download/3.0.0/kid-mainnet-3.0.0-linux-amd64
mv kid-mainnet-3.0.0-linux-amd64 kid
chmod +x kid

# check shasum - should be 0938a63c50b9ccc36e448179fe692a6f33a9602555644bda81cd4df43b70f883
sha256sum ./kid

# check version - should be Mainnet-3.0.0 (commit dac9a74443d5cdc9d9a87214e03d77d3e2f7883d)
./kid version --long

# make a directory if you haven't already
mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v3/bin

# copy this new binary
cp kid $DAEMON_HOME/cosmovisor/upgrades/v3/bin/

# this should return Mainnet-3.0.0
$DAEMON_HOME/cosmovisor/upgrades/v3/bin/kid version
```

When the upgrade height is reached, Cosmovisor will automatically restart using the new binary.

### Without Cosmovisor
To upgrade your node without using Cosmovisor,  you need to follow these steps:
1. Wait for the chain upgrade height. When it is reached, the node will stop. If the `kid` process is not automatically killed, kill it with the `kill` command.
2. Download the provided client or build it as shown earlier.
3. Start the node with the new client : `kid start --home <NODE_HOME>`

## Security

If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
