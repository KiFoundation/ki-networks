<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# Upgrade to kichain-2

This guide will provide instructions to upgrade your validator node as part of the `kichain-1`  to `kichain-2` upgrade.

This guide assumes that you have followed the step from our [tutorial](https://medium.com/ki-foundation/how-to-join-the-kichain-mainnet-5ac7160d72e3) and that you are located in your node root directory. Otherwise, please adapt commands according to your setup.

The upgrade height was set to block `6,950,000` which at the current block time should be reached by the 8th of November between 14h00 UTC and 15H00 UTC. The new client will be based on Cosmos SDK v0.42.10 and Tendermint 0.34.13.

The upgrade process consists of 2 main steps:
1. Setting the halt height **prior to the upgrade window**.
2. Exporting, migrating and restarting the nodes. These steps need to be performed **only after the chain has halted**.    


## Set the halt-height
**⚠️ Please perform this step before the upgrade window**

If you are using a service to run your node, you can set the halt height in `./kid/config/app.toml` by setting the `halt-height` variable to `6950000` and restarting your service.

Otherwise, if you've followed this [tutorial](https://medium.com/ki-foundation/how-to-join-the-kichain-mainnet-5ac7160d72e3) to start your validator, the node needs to be restarted with the `--halt-height` flag. This can be done by fetching the process ID of your running kid process:

```bash
ps ax | grep kid
```

which will output:

```bash
1234 pts/1    Sl     0:04 kid start --home kid/
24512 pts/1    S+     0:00 grep kid
```

in our case the process ID is `1234`. You need to kill it as follows:

```bash
kill 1234
```

Now you can start your node with the `--halt-height` flag as follows:

```bash
kid start --home ./kid/ --halt-height 6950000 &> ./kilogs/ki-node.log &
```

## Client upgrade

**⚠️ Please be ready during the upgrade window. These steps should be performed after the node has stopped**

Check your current `ki-tools` version

```bash
kid version --long
```

This should output
```bash
name: ki-tools
server_name: kid
client_name: kicli
version: 0.1–14-g5814c4b-Mainnet
commit: 5814c4bd64a2a2f7d388d9155c2d4b69b11fb03c
build_tags: netgo,ledger
go: go version go1.13.5 linux/amd64
```

Ensure your node has stopped. This can be done by checking the logs of the kid process, or by looking for a running kid process:

```bash
ps ax | grep kid
```

Which should ouptuts:

```bash
24512 pts/1    S+     0:00 grep kid
```

Other wise kill the process manually after the halt height is reached.

**:red_circle: If you halt height has been set in the `app.toml` file, make sure to remove it before you proceed:**

```bash
sed -i "s/halt-height\s*=\s*6950000/halt-height=0/g" ./kid/config/app.toml
```

Now ensure the change has been applied correctly

```bash
cat ./kid/config/app.toml | grep halt-height
```

which should return `halt-height=0`

**⚠️ Before you proceed ensure the node has halted! Beware of the automatic restart in case it is enabled in your service.**

Export the blockchain state

```bash
kid export --height=6950000 --home ./kid > kichain-1_genesis_export.json
```

Verify the SHA256 of the (sorted) exported genesis file:

```bash
jq -S -c -M '' kichain-1_genesis_export.json | shasum -a 256
```

**Post your hash to the Discord #validators-private channel and cross check it with the fellow validators.**

The hash agreed on by the community will be shortly after a sufficient community consensus is reached.

```bash
<HASH>
```

Update Go to version 1.16. This step depends on your setup. Once updated you can check the current version with

```bash
go version
```

which should output

```bash
go version go1.16 linux/amd64
```

Update Ki-tools to the IBC enabled version.

```bash
git clone https://github.com/KiFoundation/ki-tools.git
cd ki-tools
git checkout -b v2.0.1 tags/2.0.1
make install
```

Check ki-tools version

```bash
kid version --long
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

Migrate exported state from the old version to the new version:

```bash
kid migrate kichain-1_genesis_export.json --chain-id=kichain-2 --initial-height 6950001 --genesis-time [PLACEHOLDER] > genesis.json
```

The IBC transfer parameters will be changed before launch in genesis. To perform this change apply the following changes to the exported genesis file:

```bash
sed -i "s/\"receive_enabled\":false/\"receive_enabled\":true/g" genesis.json
sed -i "s/\"send_enabled\":false/\"send_enabled\":true/g" genesis.json
```

Update the governance parameters:

```bash
sed -i "s/\"voting_period\":\"1209600s\"/\"voting_period\":\"604800s\"/g" genesis.json
```

Verify the SHA256 of the (sorted) exported and modified genesis file:

```bash
jq -S -c -M '' genesis.json | shasum -a 256
```

**Post your hash to the Discord #validators-private channel and cross check it with the fellow validators.**

The hash agreed on by the community will be shortly after a sufficient community consensus is reached.

```bash
<HASH>
```

Backup the chain data and config directory

```bash
mkdir ./kid-backup
mv ./kid/data ./kid-backup/data
cp -R ./kid/config ./kid-backup/config

cp -R ./kicli ./kicli-backup
```

**⚠️ Be sure you have a complete backed up state of your node before proceeding with this step.**

ℹ️ The following step will point your node to create fresh configuration files (`kid/config/config.toml` and `kid/config/app.toml`). If you've heavily altered these files you might want to skip this step. However necessary keys need to be updated to fit the new client. Mainly, the new conventions for logs need to replace the old ones e.g. `log_level = 'info'`.          

```bash
mv ./kid/config/app.toml ./kid/config/app-backup.toml
mv ./kid/config/config.toml ./kid/config/config-backup.tom
```

Reset the node state
```bash
kid unsafe-reset-all --home ./kid
```

If you have custom configurations, you need to manually set them in the newly created `app.toml` and `config.toml`.

ℹ️ Make sure to add the provided seed and the community peers found in [`seed.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/seeds.txt) and [`peers.txt`](https://github.com/KiFoundation/ki-networks/blob/v0.1/Mainnet/kichain-2/peers.txt) resp.

ℹ️ Make sure to set your min-gas-price to 0.025uxki in `app.toml`.

Copy the migrated genesis file `genesis.json` to the config folder

```bash
cp genesis.json ./kid/config/
```

Your node is now ready to be started. If you've started the node with a service you can simply start the service. Otherwise, use the following command:

```bash
kid start --home ./kid/ &> ./kilogs/ki-node.log &
```

The chain will start when 67% of the voting power is up. Unless your start command outputs an error, all you need to do is to wait.


### **Migrate your keys**

The wallet keys you've created before the upgrade are not migrated automatically. You need to migrate them manually. To do so, run the following command

⚠️ Before you perform the migration read more about the different options [here](https://docs.cosmos.network/v0.42/run-node/keyring.html)

⚠️ Before you perform the migration you can use the `--dry-run` with the command below to test the migration without persisting changes.

```bash
kid keys migrate kicli/  --keyring-backend os  --home kid
```

You will be asked to enter a valid passphrase for each key in your store one by one until all keys are migrated.
