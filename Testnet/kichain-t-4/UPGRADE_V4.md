<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# kichain-t-4 V4 Upgrade

The V4 upgrade `kichain-t-4` is set to happen at height `6108800` which at current block time should be reached on `22nd of September 2022` around `13H00 UTC`.

## How to upgrade your node

Use the [provided](https://github.com/KiFoundation/ki-tools/releases/tag/4.0.0-beta) builds or build the new client yourself. A full how-to guide can be found in this [dedicated tutorial](https://github.com/KiFoundation/ki-tools#readme).

We provide static binary releases with verifiable checksums. We encourage validators to build these binaries themselves using our dedicated tutorial and check the resulting checksum.

The client version should be:
```bash
kid version --long

# name: kitools
# server_name: kid
# version: Testnet-4.0.0-alpha.1
# commit: 9627c0de235a505bd85fc6daaaa2c049d3920a31
# build_tags: netgo ledger muslc,
# go: go version go1.18 linux/amd64
```

### With Cosmovisor
To upgrade your node using Cosmovisor you need to follow these steps:

```bash
# Check that your $DAEMON_HOME is correctly set
echo $DAEMON_HOME

# Download new binary (amd64 version, please adapt if you use an arm arch)
wget https://github.com/KiFoundation/ki-tools/releases/download/4.0.0-beta/kid-testnet-4.0.0-beta-linux-amd64
mv kid-testnet-4.0.0-beta-linux-amd64 kid
chmod +x kid

# check shasum - should be 348610616b5925a19c998621e946817c37d4fa3b7f856273dca5b7795915f5fe
sha256sum ./kid

# check version - should be Testnet-4.0.0-beta (commit 9627c0de235a505bd85fc6daaaa2c049d3920a31)
./kid version --long

# make a directory if you haven't already
mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v4/bin

# copy this new binary
cp kid $DAEMON_HOME/cosmovisor/upgrades/v4/bin/

# this should return Testnet-3.0.0-beta
$DAEMON_HOME/cosmovisor/upgrades/v4/bin/kid version
```

When the upgrade height is reached, Cosmovisor will automatically restart using the new binary.

### Without Cosmovisor
To upgrade your node without using Cosmovisor,  you need to follow these steps:
1. Wait for the chain upgrade height. When it is reached, the node will stop. If the `kid` process is not automatically killed, kill it with the `kill` command.
2. Download the provided client or build it as shown earlier.
3. Start the node with the new client : `kid start --home <NODE_HOME>`

## Security

If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
