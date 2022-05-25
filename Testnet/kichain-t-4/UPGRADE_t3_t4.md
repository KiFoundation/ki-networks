<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# kichain-t-3 Upgrade

The upgrade from `kichain-t-3` to `kichain-t-4` is set to happen at height `219800` which at current block time should be reached on Monday the 6th of September 2021 between 11H and 11H30 UTC.

## How to upgrade your node
This guide will provide instructions to upgrade your validator node as part of the `kichain-t-4` upgrade.

This guide assumes that you have followed the step from our [tutorial](https://github.com/KiFoundation/ki-testnet-challenge/blob/main/tutorials/gentx.md) and that you are located in your node root directory. Otherwise, please adapt commands according to your setup.

### Set the halt-height
**Please perform this step before the upgrade window**

In `./kid/config/app.toml` set the `halt-height` variable to `219800`.
```
halt-height = 219800
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


## Upgrade steps
**Migration steps will be released prior the upgrade**

**Please be ready during the upgrade window. These steps should be performed after the node has stoped**

Check your current ki-tools version

```bash
kid version --long
```

This should output

```
name: kitools
server_name: kid
version: testnet-ibc-e3febac1a22147073549b0042adc87d33bfa6e39-Testnet-IBC
commit: e3febac1a22147073549b0042adc87d33bfa6e39
build_tags: netgo,ledger
go: go version go1.16 linux/amd64
build_deps:
....
```

If you have set a halt height, check halt height:

```bash
cat ./kid/config/app.toml | grep halt-height
```

This shoud output

```bash
halt-height=219800
```

Other wise kill the process manually after the halt height is reached.

⚠️ Before you proceed ensure the node has halted! Beware of the automatic restart in case it is enabled in your service.

Backup the chain data and config directory

```bash
cp -r ./kid ./kid-kichain-t-3-backup
```

Export the blockchain state

```bash
kid export --height=219800 --home ./kid > kichain-t-3_genesis_export.json
```

Verify the SHA256 of the (sorted) exported genesis file:

```bash
jq -S -c -M '' kichain-t-3_genesis_export.json | shasum -a 256
```

Update the IBC parameters:

```bash
sed -i "s/\"receive_enabled\":false/\"receive_enabled\":true/g" kichain-t-3_genesis_export.json
sed -i "s/\"send_enabled\":false/\"send_enabled\":true/g" kichain-t-3_genesis_export.json
```

Update the governance parameters:

```bash
sed -i "s/\"voting_period\":\"1209600s\"/\"voting_period\":\"7200s\"/g" kichain-t-3_genesis_export.json
```

Update chain id

```bash
sed -i "s/\"kichain-t-3\"/\"kichain-t-4\"/g" kichain-t-3_genesis_export.json
```

Update next channel-id

Find the next channel sequence (use one of the following):

```bash
prev=$(cat kichain-t-3_genesis_export.json | jq .app_state.ibc.channel_genesis.ack_sequences[] | jq 'map(split("-"))[0][1] | tonumber' | sort -n | tail -1) && next=$(($prev+1)) && echo $next
```

or

```bash
prev=$(cat kichain-t-3_genesis_export.json | jq .app_state.ibc.channel_genesis.ack_sequences[].channel_id | cut -d'-' -f2 | cut -d'"' -f1 | sort -n | tail -1) && next=$(($prev+1)) && echo $next
```

This will output an integer which you can verify by comparing it against the community findings on Discord.

```bash
sed -i "s/\"next_channel_sequence\":\"0\"/\"next_channel_sequence\":\"$next\"/g" kichain-t-3_genesis_export.json
```

```bash
mv kichain-t-3_genesis_export.json genesis.json

```

Verify the SHA256 of the (sorted) exported genesis file:

```bash
jq -S -c -M '' genesis.json | shasum -a 256
```

Post your hash to the Discord #testnet-challenge channel and cross check it with the fellow validators.

⚠️ Be sure you have a complete backed up state of your node before proceeding with this step.

Backup your configurations

```bash
cp ./kid/config/config.toml ./kid/config/config.toml.kichain-t-3.bak
cp ./kid/config/app.toml ./kid/config/app.toml.kichain-t-3.bak
```

Reset the node state

```bash
kid unsafe-reset-all --home ./kid
```

Copy the migrated genesis file `genesis.json` to the config folder

```bash
cp genesis.json ./kid/config/
```

Your node is now ready to be started. If you've started the node with a service you can simply start the service. Otherwise, use the following command:

```bash
kid start --home ./kid/ &> ./kilogs/ki-node.log &
```

The chain will start when 67% of the voting power is up. Unless your start command outputs an error, all you need to do is to wait.

## Genesis hash reference

### After export

```
3377744f812972a82dac855e357046152905d6a407bd526b55e45b874895d817
```

### After parameters update

```
83e8d8b9b883f1d478fb8d9f0e32c138904d0cbc30e939fa0490f988fd0b78d7
```

## Security
If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
