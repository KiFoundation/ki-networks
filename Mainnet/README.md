<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# Ki Mainnet
**Current Version :** kichain-1

## How to join   
**Note :** A fresh install of Ubuntu 18.04 is recommended for this deployment.

To join the Kichain Mainnet, you need first to install both `golang` and `ki-tools`. You can do it by following the dedicated tutorial that can be found [here](https://github.com/KiFoundation/ki-tools/blob/master/README.md).

You will also need a couple of basic tools such as `curl` and `vim` (or whatever tools that can replace them). Go ahead and install them :
```
sudo apt install vim
sudo apt install curl
```

Once done, create a folder to contain your node information:
```
export NODE_ROOT= <location of your choice>
mkdir -p $NODE_ROOT/kid $NODE_ROOT/kicli $NODE_ROOT/kilogs
cd $NODE_ROOT
```

Initiate the needed configuration files using the `unsafe-reset-all`command:
```
kid unsafe-reset-all --home ./kid
```

Copy the genesis file to the `./kid/config/` folder:
```
curl https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Mainnet/kichain-1/genesis.json > ./kid/config/genesis.json

```

Once done, you need to give a name to your node and indicate the seed server to which it should connect to join the network. All of this can be done in the `config.toml` that can be found in the `./kid/config/` directory. Change the default `moniker` to whatever you want (the default name is the machine name). Then provide in in the field `persistent_peers` the address of one of the following persistent peers.

```
persistent_peers="14cbbaf7c759556c048600653eeacf45eb681401@15.188.159.246:26656,4fafa0f412c1e7a361c5d61ad6224cc8f19ddde4@51.210.102.3:26656,dd891b043bc970e2a15def741ca3b326e6c6d02e@51.195.41.88:26656"
```

An important thing to set before you launch the node is the `minimum-gas-price` that indicates the minimum gas price that a transaction must meet in order to be accepted in your node's mempool. This protect you node from being spammed by empty transactions. To change this value, change the value of `minimum-gas-price` to the recommended value of `0.025uxki` in the `$NODE_ROOT$/kid/config/app.toml` file.

Now that the node is configured, you can start it.

```
kid start --home ./kid/ &> ./kilogs/ki-node.log &

```
This command will start the block synchronisation process where your node retrieves the current state of the blockchain from the other nodes. The process output is redirected to the `ki-node.log`log file. It can be visualised as follows:

```
tail -f kilogs/ki-node.log
```
and it looks like this:

```
I[2020-02-12|08:52:14.376] Executed block            module=state height=1 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.393] Committed state            module=state height=1 txs=0 appHash=HASHHASHASH...
I[2020-02-12|08:52:14.442] Executed block            module=state height=2 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.449] Committed state            module=state height=2 txs=0 appHash=HASHHASHASH...
I[2020-02-12|08:52:14.474] Executed block            module=state height=3 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.481] Committed state            module=state height=3 txs=0 appHash=HASHHASHASH...
I[2020-02-12|08:52:14.507] Executed block            module=state height=4 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.514] Committed state            module=state height=4 txs=0 appHash=HASHHASHASH...
I[2020-02-12|08:52:14.539] Executed block            module=state height=5 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.547] Committed state            module=state height=5 txs=0 appHash=HASHHASHASH...
I[2020-02-12|08:52:14.572] Executed block            module=state height=6 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.579] Committed state            module=state height=6 txs=0 appHash=HASHHASHASH...
...
```  

**Note :** You will need to wait for the end of the synchronisation process before proceeding to the validator creation.

The previous steps will start the node in a relay mode. That is, it is relaying transactions and blocks, but note validating new blocks. To enable the validation process, you need to create and declare your own validator. Start by create your validator wallet by generating a new key pair as follows (Here we call it `wallet-1` but you can call it whatever you want):

```
kicli keys add wallet-1 --home ./kicli/
```

Enter your password twice and then **<span style="color:red">save </span>** the generated addresses, keys and **<span style="color:red">passphrase</span>**.

Launching a validator requires a wallet with a positive XKI balance. To get XKI, contact us by [email](http://contact@foundation.ki) or simply join our [Slack](https://join.slack.com/t/kiecosystem/shared_invite/zt-d4qi2h4y-anCgusTdfchTpXQ8KzGTXA) space. Once acquired, your tokens can be managed from within the [Ki Desktop Wallet](https://github.com/KiFoundation/ki-desktop-wallet/releases).

Now that your wallet has a positive balance, you can create your validator
through a staking transaction:

```
kicli tx staking create-validator \
            --commission-max-change-rate=0.1 \
            --commission-max-rate=0.1 \
            --commission-rate=0.1 \
            --min-self-delegation=1 \
            --amount=100000000uxki \
            --pubkey `kid tendermint show-validator --home ./kid/` \
            --moniker=<YOUR VALIDATOR NAME> \
            --chain-id=kichain-1 \
            --gas-prices="0.025uxki" \
            --from=wallet-1 \
            --home ./kicli/
```

To know more about the various possible configurations and on editing your validator, please refer to the dedicated [documentation](https://github.com/cosmos/gaia/blob/master/docs/validators/validator-setup.md#edit-validator-description). Once this transaction has passed, and if the bonded amount is sufficient to be in the active validator list, your validator will automatically start validating new blocks.

## Security
If you discover a security vulnerability in this project, please report it to security@foundation.ki. We will promptly address all security vulnerabilities.
