# kichain-2 V5 Upgrade

The V5 upgrade `kichain-2` is set to happen at height `14340215` which at current block time should be reached on `23rd of March 2023` around `13H00 UTC`.

## How to upgrade your node

Use the [provided](https://github.com/KiFoundation/ki-tools/releases/tag/5.0.0) builds or build the new client yourself. A full how-to guide can be found in this [dedicated tutorial](https://github.com/KiFoundation/ki-tools#readme).

We provide static binary releases with verifiable checksums. We encourage validators to build these binaries themselves using our dedicated tutorial and check the resulting checksum.

Note: this version requires `go 1.19+`

The client version should be:
```bash
kid version --long

# name: kitools
# server_name: kid
# version: Mainnet-5.0.0
# commit: 7cd2cf88fb87c9695d7eea9107eb6fe5e1ec93fb
# build_tags: netgo ledger muslc,
# go: go version go1.19.7 linux/amd64
```

### With Cosmovisor
To upgrade your node using Cosmovisor you need to follow these steps:

```bash
# Check that your $DAEMON_HOME is correctly set
echo $DAEMON_HOME

# Download new binary (amd64 version, please adapt if you use an arm arch)
wget https://github.com/KiFoundation/ki-tools/releases/download/5.0.0/kid-mainnet-5.0.0-linux-amd64
mv kid-mainnet-5.0.0-linux-amd64 kid
chmod +x kid

# check shasum - should be 80bb65e53189cf7ce941fe2253cde57696930d691ce7e8b69c2a7a4fc1e13724
sha256sum ./kid

# check version - should be Mainnet-5.0.0 (commit 7cd2cf88fb87c9695d7eea9107eb6fe5e1ec93fb)
./kid version --long

# make a directory if you haven't already
mkdir -p $DAEMON_HOME/cosmovisor/upgrades/v5/bin

# copy this new binary
cp kid $DAEMON_HOME/cosmovisor/upgrades/v5/bin/

# this should return Mainnet-5.0.0
$DAEMON_HOME/cosmovisor/upgrades/v5/bin/kid version
```

When the upgrade height is reached, Cosmovisor will automatically restart using the new binary.

### Without Cosmovisor
To upgrade your node without using Cosmovisor,  you need to follow these steps:
1. Wait for the chain upgrade height. When it is reached, the node will stop. If the `kid` process is not automatically killed, kill it with the `kill` command.
2. Download the provided client or build it as shown earlier.
3. Start the node with the new client : `kid start --home <NODE_HOME>`

## Security

If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
