# kichain-t-2 Upgrade

The upgrade from `kichain-t-2` to `kichain-t-3` is set to happen at height 64800 which at current block time should be reached on Wednesday the 25th of August between 10H30 and 11H30 UTC.

## How to upgrade your node
This guide will provide instructions to upgrade your validator node as part of the `kichain-t-3` upgrade.

This guide assumes that you have followed the step from our [tutorial](https://github.com/KiFoundation/ki-testnet-challenge/blob/main/tutorials/gentx.md) and that you are located in your node root directory. Otherwise, please adapt commands according to your setup.

### Set the halt-height
**Please perform this step before the upgrade window**

In `./kid/config/app.toml` set the `halt-height` variable to `64800`.
```
halt-height = 64800
```

Restart your node. If you are using a service, it suffices to restart it. Otherwise, this can be done by fetching the process ID of your running kid process:

```bash
ps ax | grep kid
```
which will output:

```bash
1234 pts/1    Sl     0:04 kid start --home kid/
24512 pts/1    S+     0:00 grep kid
```
in our case the process ID is `1234`. You need to kill it as follows:

```bash
kill 1234
```
Start your node again

```bash
kid start --home ./kid/ &> ./kilogs/ki-node.log &
```


## Client upgrade
**Please be ready during the upgrade window. These steps should be performed after the node has stoped**

Check your current ki-tools version

```bash
kid version --long
```

This should output  

```bash
server_name: kid
client_name: kicli
version: 0.1-13-g711e19e-Testnet
commit: 711e19ebbb516ae6d38c638634c4bef05201be72
build_tags: netgo,ledger
go: go version go1.13.5 linux/amd64
```

If you have set a halt height, check halt height:

```bash
cat ./kid/config/app.toml | grep halt-height
```

This shoud output

```bash
halt-height=64800
```

Other wise kill the process manually after the halt height is reached.

⚠️ Before you proceed ensure the node has halted! Beware of the automatic restart in case it is enabled in your service.

Backup the chain data and config directory  

```bash
cp -r ./kid ./kid-backup
```

Export the blockchain state

```bash
kid export --height=64800 --home ./kid > kichain-t-2_genesis_export.json
```

Verify the SHA256 of the (sorted) exported genesis file:

```bash
jq -S -c -M '' kichain-t-2_genesis_export.json | shasum -a 256
```
Post your hash to the Discord #testnet-challenge channel and cross check it with the fellow validators.

The hash agreed on by the community is: 
```bash
67d4310caac32db6256430ac0290dd697ff94ab8317cea00cdd78b6612aac95d
```


Update Go to at least v1.16. This step depends on your setup. Once updated you can check the current version with

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
git checkout testnet-ibc
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
version: testnet-ibc-e3febac1a22147073549b0042adc87d33bfa6e39-Testnet-IBC
commit: e3febac1a22147073549b0042adc87d33bfa6e39
build_tags: netgo,ledger
go: go version go1.16 linux/amd64
build_deps:
....
```

Migrate exported state from the old version to the new version:

```bash
kid migrate kichain-t-2_genesis_export.json --chain-id=kichain-t-3 --initial-height 64801 > genesis.json
```

Verify the SHA256 of the (sorted) exported genesis file:

```bash
jq -S -c -M '' genesis.json | shasum -a 256
```

Post your hash to the Discord #testnet-challenge channel and cross check it with the fellow validators.

The hash agreed on by the community is: 
```bash
875649fe148de10c717e970315d8b05b5b797506e0502c24fd706e3c739e69cc
```

⚠️ Be sure you have a complete backed up state of your node before proceeding with this step.


Backup your configurations
```bash
mv ./kid/config/config.toml ./kid/config/config.toml.kichain-t-2.bak
mv ./kid/config/app.toml ./kid/config/app.toml.kichain-t-2.bak
```
Reset the node state

```bash
kid unsafe-reset-all --home ./kid
```

If you have custom configurations, you need to manually set them in the newly created `app.toml` and `config.toml`.

Copy the migrated genesis file `genesis.json` to the config folder
```bash
cp genesis.json ./kid/config/
```

Your node is now ready to be started. If you've started the node with a service you can simply start the service.
Otherwise, use the following command:

```bash
kid start --home ./kid/ &> ./kilogs/ki-node.log &
```

The chain will start when 67% of the voting power is up. Unless your start command outputs an error, all you need to do is to wait.

### Migrate your keys
The wallet keys you've created before the upgrade are not migrated automatically. You need to migrate them manually. To do so, run the following command

⚠️ Before you perform the migration read more about the different options [here](https://docs.cosmos.network/v0.42/run-node/keyring.html)

⚠️ Before you perform the migration you can use the `--dry-run` with the command below to test the migration without persisting changes.

```bash
kid keys migrate kicli/  --keyring-backend os  --home kid
```
You will be asked to enter a valid passphrase for each key in your store one by one until all keys are migrated.

## Security
If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
