# kichain-t-2

## TLDR

On this repository, you can find:
- [the genesis file](./genesis.json) (hash: `ae0e4eeb6aebb0bcb0356279d851b2ddd5631a678045da1d2cd0592146d6f1b6`)
- [peers](./peer-nodes.txt)
- [seeds](./seed-nodes.txt)

## Start your node (Validators registered in genesis)
Thank you for submitting a gentx!
This guide will provide instructions to setup your validator for the testnet challenge.

**Please have your validator up and ready by August 18 2021, at 13:00 UTC.**

This guide assumes that you have followed the step from our [tutorial](https://github.com/KiFoundation/ki-testnet-challenge/blob/main/tutorials/gentx.md). Otherwise, please adapt commands according to your setup.

To download the Genesis:
```bash
curl https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Testnet/kichain-t-2/genesis.json > ./kid/config/genesis.json
```
```bash
sha256sum ./kid/config/genesis.json
ae0e4eeb6aebb0bcb0356279d851b2ddd5631a678045da1d2cd0592146d6f1b6  ./kid/config/genesis.json
```

You need to add seeds from [this file](./seed-nodes.txt) and persistent peers (and add your own if you want) from [this file](./peer-nodes.txt) to this config file:
```bash
$NODE_ROOT/kid/config/config.toml
```

To do it automatically:
```bash
sed -i.bak -e "s/^seeds *=.*/seeds = \"$(curl -s https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Testnet/kichain-t-2/seed-nodes.txt | tr '\n' ', ' | sed 's/,$//')\"/; s/^persistent_peers *=.*/persistent_peers = \"$(curl -s https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Testnet/kichain-t-2/peer-nodes.txt | tr '\n' ', ' | sed 's/,$//')\"/" $NODE_ROOT/kid/config/config.toml
```

To launch your node:
```bash
kid start --home ./kid/ &> ./kilogs/ki-node.log &
```

If you need any help, please ask on Discord

## Start your node (Validators joining after genesis time)
Note: If you have already tried to join the testnet in genesis, you can use the created setup. Please start with step 4.

1. Install `Go` and `ki-tools` and create the node directories as shown in this [tutorial](https://github.com/KiFoundation/ki-testnet-challenge/blob/main/tutorials/gentx.md).

2. Initiate the node files:

```bash
kid unsafe-reset-all --home ./kid
```

3. Create the validator account. Do not forget to save the mnemonic.

```bash
kicli keys add <wallet-name> --home ./kicli/
```

4. Share the address of the created validator wallet on Discord (#testnet-challenge channel) to get some tokens to create your validator. Other users or team members will be able to send you few TKIs **after** the launch.

5. Download the Genesis:

```bash
curl https://raw.githubusercontent.com/KiFoundation/ki-networks/v0.1/Testnet/kichain-t-2/genesis.json > ./kid/config/genesis.json
```
```bash
sha256sum ./kid/config/genesis.json
ae0e4eeb6aebb0bcb0356279d851b2ddd5631a678045da1d2cd0592146d6f1b6  ./kid/config/genesis.json
```

You can find persistent peers in [this file](./peer-nodes.txt)

**The following needs to be done after the chain launch**

6. Start your node:

```bash
kid start --home ./kid/ &> ./kilogs/ki-node.log &
```

Note : You will need to wait for the end of the synchronization process before proceeding to the validator creation.

7. Create you validator:

```bash
kicli tx staking create-validator \
            --commission-max-change-rate=0.1 \
            --commission-max-rate=0.1 \
            --commission-rate=0.1 \
            --min-self-delegation=1 \
            --amount=5000000utki \
            --pubkey `kid tendermint show-validator --home ./kid/` \
            --moniker=<YOUR VALIDATOR NAME> \
            --chain-id=kichain-t-2 \
            --gas-prices=0.025utki \
            --from=<wallet-name> \
            --home ./kicli/
```

## Additional tips
To launch your node using a service file:
```bash
sudo tee /etc/systemd/system/kichaind.service > /dev/null <<EOF
[Unit]
Description=KiChain Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which kid) start --home $NODE_ROOT/kid/
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable kichaind
sudo systemctl start kichaind
```

To view the service log:
```bash
journalctl -n 100 -f -u kichaind
```

If you need any help, please ask on Discord
