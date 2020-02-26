<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# Ki Testnet
**Current Version :** KiChain-t

## How to join   
**Note :** A fresh install of Ubuntu 18.04 is recommended for this deployment.

To join the Kichain testnet, you need first to install both `golang` and `ki-tools`. You can do it by following the dedicated tutorial that can be found [here](https://github.com/KiFoundation/ki-tools/blob/master/README.md).

You will also need a couple of basic tools such as `curl` and `vim` (or whatever tools that can replace them). Go ahead and install them :
```
sudo apt install vim
sudo apt install curl
```

Once done, create a folder to contain your node information:
```
mkdir -p node-1/kid node-1/kicli node-1/kilogs
cd node-1
```

Initiate the needed configuration files using the `unsafe-reset-all`command:
```
kid unsafe-reset-all --home ./kid
```

Copy the genesis file to the `./kid/config/` folder:
```
curl https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Testnet/Kichain-t/genesis.json > ./kid/config/genesis.json

```

Once done, you need to give a name to your node and indicate the seed server to which it should connect to join the network. All of this can be done in the `config.toml` that can be found in the `./kid/config/` directory. Change the default `moniker` to whatever you want (the default name is the machine name). Then provide in in the field `persistent_peers` the address of one of the following persistent peers.

```
persistent_peers:
2908d4b48cb112fc24c2016f142fe6b351ffdffd@94.23.3.107:36656
```

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
I[2020-02-12|08:52:14.393] Committed state            module=state height=1 txs=0 appHash=24184D013C8A107964BCD4588A6C4E7955D8F98DCF3013AB8B82ED4F790E4AE7
I[2020-02-12|08:52:14.442] Executed block            module=state height=2 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.449] Committed state            module=state height=2 txs=0 appHash=FF0EE6ED6CDD364A1C244BB7DFE7D61DFAD9046EC9D4322BAABF10942D829385
I[2020-02-12|08:52:14.474] Executed block            module=state height=3 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.481] Committed state            module=state height=3 txs=0 appHash=535C3FC5CF880AF9B98E28AB410D401B46F561432CF667EBE59A9BE6274331C3
I[2020-02-12|08:52:14.507] Executed block            module=state height=4 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.514] Committed state            module=state height=4 txs=0 appHash=4303336C99417D4D0242E84003411C30A30B08421C6858B847736A6437688D9B
I[2020-02-12|08:52:14.539] Executed block            module=state height=5 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.547] Committed state            module=state height=5 txs=0 appHash=B29267030784C9B9461FBBCF8E539144AD5ADB2CCD32BD81A99C8EDD015A096F
I[2020-02-12|08:52:14.572] Executed block            module=state height=6 validTxs=0 invalidTxs=0
I[2020-02-12|08:52:14.579] Committed state            module=state height=6 txs=0 appHash=5BD981B1A3D238BDD03BD2396C56AF64F7807DC6736A2F217E2D231EBA63B816
...
```  

**Note :** You will need to wait for the end of the synchronisation process before proceeding to the validator creation.

The previous steps will start the node in a relay mode. That is, it is relaying transactions and blocks, but note validating new blocks. To enable the validation process, you need to create and declare your own validator. Start by create your validator wallet by generating a new key pair as follows (Here we call it `wallet-1` but you can call it whatever you want):

```
kicli keys add wallet-1 --home ./kicli/
```

Enter your password twice and then **<span style="color:red">save </span>** the generated addresses, keys and **<span style="color:red">passphrase</span>**.

If you have another wallet with testnet tokens (TKI), then feed the newly created wallet with some tokens. Otherwise, join our public slack channel [#ki-testnet](https://slack.com/share/IUHJ5QN8G/umeKukLeDdj2cE11ehZGqFat/enQtOTY5NjE3ODM4Mjg4LTZlYWVjZmI4NjgwMzIzZjJjMGY4MzQzNDQ2NDM5NmVjY2U5ZWEzNWJhYzI2MTg4NzJjYTFiNzYwODFkMzYzNGM), ~~or head to [faucet-testnet.blockchain.ki]()~~, and we will show you some love ...

Now that your wallet has a positive balance, you can create your validator
through a staking transaction:

```
kicli tx staking create-validator \
                  --commission-max-change-rate=0.1 \
                  --commission-max-rate=0.1 \
                  --commission-rate=0.1 \
                  --min-self-delegation=1 \
                  --amount=100000000tki \
                  --pubkey `kid tendermint show-validator --home ./kid/` \
                  --moniker=<YOUR VALIDATOR NAME> \
                  --chain-id=KiChain-t \
                  --from=wallet-1 \
                  --home ./kicli/
```

To know more about the various possible configurations of your validator, please refer to the dedicated [documentation](http://). Once this transaction has passed, and if the bonded amount is sufficient to be in the active validator list, your validator will automatically start validating new blocks.



## How to upgrade
...
