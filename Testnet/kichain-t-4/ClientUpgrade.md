# kichain-t-3 Upgrade

The upgrade of `kichain-t-4` is set to happen at height `951150` which at current block time should be reached on Monday the 28th of October between 11H and 11H30 UTC. This upgrade is stop and start client update without genesis migration.    

## How to upgrade your node
This guide will provide instructions to upgrade your validator node as part of the `kichain-t-4` upgrade.

This guide assumes that you have followed the step from our [tutorial](https://github.com/KiFoundation/ki-testnet-challenge/blob/main/tutorials/gentx.md) and that you are located in your node root directory. Otherwise, please adapt commands according to your setup.

### Set the halt-height
**Please perform this step before the upgrade window**

In `./kid/config/app.toml` set the `halt-height` variable to `951150`.
```
halt-height = 951150
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
**Please be ready during the upgrade window. These steps should be performed after the node has stoped**

If you have set a halt height, the node will halt automatically. The halt height can be checked as follows:

```bash
cat ./kid/config/app.toml | grep halt-height
```

This shoud output

```bash
halt-height=951150
```

Other wise kill the process manually after the halt height is reached. You can ensure the node hs stopped using the logs or by curling the node rpc locally :

```bash
curl localhost:<RPC-PORT>
```

The command above shoul fail as the node is not supposed to be listening anymore.


⚠️ Before you proceed ensure the node has halted! Beware of the automatic restart in case it is enabled in your service.


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


Clone and build the new client:

```bash
git clone https://github.com/KiFoundation/ki-tools.git
git checkout ibc-v1
make install
```

Check the isntalled ki-tools version

```bash
kid version --long
```

This should output
```
name: kitools
server_name: kid
version: ibc-v1-59cae5f6c094fffb4bb483f7adbcd9838ceab852-Testnet-IBC
commit: 59cae5f6c094fffb4bb483f7adbcd9838ceab852
build_tags: netgo,ledger
go: go version go1.16 linux/amd64
build_deps:
...
cosmos_sdk_version: v0.42.10
```

Backup the chain data and config directory

```bash
cp -r ./kid ./kid-kichain-t-4-backup
```

⚠️ Be sure you have a complete backed up state of your node before proceeding with this step.


Your node is now ready to be started. If you've started the node with a service you can simply start the service. Otherwise, use the following command:

```bash
kid start --home ./kid/ &> ./kilogs/ki-node.log &
```

The chain will start when 67% of the voting power is up. Unless your start command outputs an error, all you need to do is to wait.

## Security
If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
