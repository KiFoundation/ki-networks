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

To set the halt height the node need to be restarted with the `--halt-height` flag. This can be done by fetching the process ID of your running kid process:

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
The client upgrade steps as well as the target client version will be shared soon.
