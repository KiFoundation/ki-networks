# kichain-t-2 Upgrade

The upgrade from `kichain-t-3` to `kichain-t-4` is set to happen at height `219800` which at current block time should be reached on Monday the 6th of September between 11H and 11H30 UTC.

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

## Security
If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
