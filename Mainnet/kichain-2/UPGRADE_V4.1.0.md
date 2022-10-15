<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# kichain-2 V4.1.0 patch

This patch fixes [Dragonberry Vulnerability](https://forum.cosmos.network/t/ibc-security-advisory-dragonberry/7702) and is non consensus breaking. Validators can change their binary at any time.

## How to upgrade your node

Use the [provided](https://github.com/KiFoundation/ki-tools/releases/tag/4.1.0) builds or build the new client yourself. A full how-to guide can be found in this [dedicated tutorial](https://github.com/KiFoundation/ki-tools#readme).

We provide static binary releases with verifiable checksums. We encourage validators to build these binaries themselves using our dedicated tutorial and check the resulting checksum.

The client version should be:
```bash
kid version --long

# name: kitools
# server_name: kid
# version: Mainnet-4.1.0
# commit: b5732b570d1de928e1a37a05c6b171e3ca06d6e6
# build_tags: netgo ledger muslc,
# go: go version go1.18 linux/amd64
```

### With Cosmovisor
To upgrade your node using Cosmovisor you need to follow these steps:

```bash
# Check that your $DAEMON_HOME is correctly set
echo $DAEMON_HOME

# Download new binary (amd64 version, please adapt if you use an arm arch)
wget https://github.com/KiFoundation/ki-tools/releases/download/4.1.0/kid-mainnet-4.1.0-linux-amd64
mv kid-mainnet-4.1.0-linux-amd64 kid
chmod +x kid

# check shasum - should be 973e02b89685dc8cef3a5ab6d357cafbcadd5c5c35fbd67c88a39ce2a085817e
sha256sum ./kid

# check version - should be Mainnet-4.1.0 (commit b5732b570d1de928e1a37a05c6b171e3ca06d6e6)
./kid version --long

# stop cosmovisor
sudo systemctl stop cosmovisor

# copy this new binary
cp kid $DAEMON_HOME/cosmovisor/upgrades/v4/bin/

# this should return Mainnet-4.1.0
$DAEMON_HOME/cosmovisor/upgrades/v4/bin/kid version

# start cosmovisor
sudo systemctl start cosmovisor

# check you are running the new binary - should return Mainnet-4.1.0
curl -s http://127.0.0.1:26657/abci_info | jq .result.response.version
```

### Without Cosmovisor
To upgrade your node without using Cosmovisor,  you need to follow these steps:
1. Download the provided client or build it as shown earlier.
2. Check that `kid version` returns "Mainnet-4.1.0"
3. Restart your validator
4. Check you are running the new binary: `curl -s http://127.0.0.1:26657/abci_info | jq .result.response.version`


## Security

If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
