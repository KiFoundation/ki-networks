<p align="right">
    <img width=150px src="https://wallet-testnet.blockchain.ki/static/img/icons/ki-chain.png" />
</p>

# Ki Testnet
**Current Version :** Kichain-t

## How to join   
**Note :** A fresh install of Ubuntu 18.04 is recommended for this install.

To join the Kichain testnet, you need first to install both `golang` and `ki-tools`. You can do it by following the dedicated tutorial that can be found [here](https://github.com/KiFoundation/ki-tools/blob/master/README.md).

Once done, create a folder to contain your node information:
```
mkdir -p node-1/kid node-1/kicli
```

Initiate the needed configuration files using the `unsafe-reset-all`command:
```
kid unsafe-reset-all --home node-1/kicli/
```

Copy the genesis file to the `node-1/kid/config/` folder:
```
curl https://raw.githubusercontent.com/KiFoundation/ki-networks/master/testnets/kichain-t/genesis.json > node-1/kid/config/genesis.json

```

Once done, you need to give a name to your node and indicate the seed server to which it should connect to join the network. All of this can be done in the `config.toml` that can be found in the `node-1/kid/config/` directory. Change the default `moniker` given in `line 11` to whatever you want (the default name is the machine name). Then provide in `line 163` the address of one oh the following persistent server.

`persistent_peers` :

```
2908d4b48cb112fc24c2016f142fe6b351ffdffd@94.23.3.107:36656
```

Now that the node is configured, you can start it.

```
kid start --home /node-1/kid/

```

The previous steps will start the node in a relay mode. That is, it is relaying transactions and blocks, but note validating new blocks. To enable the validation process, you need to create and declare your own validator. Start by create your validator wallet by generating a new key pair as follows (Here we call it `wallet-1` but you can git it whatever you want):

```
kicli keys add wallet-1 --home node-1/kicli/
```

Enter your password twice and then **<span style="color:red">save </span>** the generated addresses, keys and **<span style="color:red">passphrase</span>**.

If you have another wallet with testnet tokens (TKI), then feed the newly created wallet with some tokens. Otherwise head to [faucet-testnet.blockchain.ki](faucet-testnet.blockchain.ki), enter the address of the newly created wallet and we will show you some love ...

Now that your wallet has a positive balance, you can create your validator
through a staking transaction:

```
kicli tx staking create-validator \
                  --commission-max-change-rate=0.1 \
                  --commission-max-rate=0.1
                  --commission-rate=0.1
                  --min-self-delegation=1
                  --amount=100000000tki
                  --pubkey `kid tendermint show-validator --home node-1/kid/`
                  --moniker=node-1
                  --chain-id=kichain
                  --from=wallet-1
                  --home node-1/kid/
```

To know more about the various possible configurations of your validator, please refer to the dedicated [documentation](http://)

## How to upgrade
...
