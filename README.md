# PlanQ

# PlanQ Main

```
NODENAME=Mad
```
```
PLANQ_PORT=17
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export PLANQ_CHAIN_ID=planq_7070-2" >> $HOME/.bash_profile
echo "export PLANQ_PORT=${PLANQ_PORT}" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt install curl build-essential git wget jq make gcc tmux chrony -y
```
```
cd $HOME
git clone https://github.com/planq-network/planq.git && cd planq
git fetch origin --tags
git checkout v1.0.2
make install
```
```
planqd version --long
```
```
planqd config chain-id $PLANQ_CHAIN_ID
planqd config node tcp://localhost:${PLANQ_PORT}657
planqd config keyring-backend test
```
```
planqd init $NODENAME --chain-id $CHAIN_ID
```
```
curl -o $HOME/.planqd/config/genesis.json "https://github.com/planq-network/networks/blob/main/mainnet/genesis.json"
```
```
planqd keys add $WALLET
```
```
```
```
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:17658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:17657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:1760\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:17656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \»:17660\"%" $HOME/.planqd/config/config.toml && sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \»0.0.0.0:17090\"%; s%^address = \"0.0.0.0:9091\"%address = \»0.0.0.0:17091\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1717\"%" $HOME/.planqd/config/app.toml && sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:17657\"%" $HOME/.planqd/config/client.toml
```
```
pruning="nothing"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.planqd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.planqd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.planqd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.planqd/config/app.toml
```
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.planqd/config/config.toml
```
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0001aplanq\"/;" ~/.planqd/config/app.toml
```
```
export SEEDS="dd2f0ceaa0b21491ecae17413b242d69916550ae@135.125.247.70:26656,0525de7e7640008d2a2e01d1a7f6456f28f3324c@51.79.142.6:26656,7c10b1a106a512976e8d71effe5c086327458eef@35.200.183.35:26656"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/" ~/.planqd/config/config.toml
```
```
wget -qO $HOME/.planqd/config/addrbook.json "https://github.com/planq-network/networks/blob/main/mainnet/addrbook.json"
```
```
sudo tee /etc/systemd/system/planqd.service > /dev/null <<EOF
[Unit]
Description=planq
After=network-online.target

[Service]
User=$USER
ExecStart=$(which planqd) start --home $HOME/.planqd
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```                                                               
```                                                               
sudo systemctl daemon-reload
sudo systemctl enable planqd
sudo systemctl restart planqd && sudo journalctl -u planqd -f -o cat
```
```
source $HOME/.bash_profile
```
```                                                               
planqd status 2>&1 | jq .SyncInfo
```
```  
PLANQ_WALLET_ADDRESS=$(planqd keys show $WALLET -a)
PLANQ_VALOPER_ADDRESS=$(planqd keys show $WALLET --bech val -a)
echo 'export PLANQ_ADDRESS='${PLANQ_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export PLANQ_VALOPER_ADDRESS='${PLANQ_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```  
planqd keys list
```  
```  
```  
```  
planqd query bank balances $PLANQ_WALLET_ADDRESS
```  
```  
planqd tx staking create-validator \
  --amount 2200000000000000000aplanq \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.1" \
  --commission-rate "0.08" \
  --min-self-delegation "1" \
  --pubkey  $(planqd tendermint show-validator) \
  --identity=B08700D1C239CD1A \
  --website="https://github.com/madnoder" \
  --details="Cosmos validator" \
  --moniker $NODENAME \
  --chain-id $PLANQ_CHAIN_ID \
  --security-contact="vova1node@gmail.com" \
  --gas 1000000
```                  

```
planqd tx staking delegate $PLANQ_VALOPER_ADDRESS 10000aplanq --from=$WALLET --chain-id=$PLANQ_CHAIN_ID --gas auto
```
```
planqd tx staking edit-validator \
  --new-moniker="Mad as a hatter" \
  --chain-id $PLANQ_CHAIN_ID \
  --from=plq1tsrnkfqelmz4ggu2pygdxww3hupqv9x49nl9mj
```
```
planqd tx distribution withdraw-rewards plqvaloper1tsrnkfqelmz4ggu2pygdxww3hupqv9x4mdjlar --from=plq1tsrnkfqelmz4ggu2pygdxww3hupqv9x49nl9mj --commission --chain-id=$PLANQ_CHAIN_ID --gas="1000000" --gas-prices="30000000000aplanq" --gas-adjustment="1.15"
```
```
for i in {1..10}; do quicksilverd status --node https://quicksilver-testnet.rpc.kjnodes.com/ | jq .SyncInfo.latest_block_height && quicksilverd status | jq .SyncInfo.latest_block_height; sleep 6; done
```
```
sudo systemctl stop planqd
cp $HOME/.planqd/data/priv_validator_state.json $HOME/.planqd/priv_validator_state.json.backup
rm -rf $HOME/.planqd/data
```
```
curl -L https://snapshot.planq.indonode.net/planq-snapshot-2022-12-31.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.planqd
mv $HOME/.planqd/priv_validator_state.json.backup $HOME/.planqd/data/priv_validator_state.json
```
```
sudo systemctl restart planqd && journalctl -u planqd -f --no-hostname -o cat
```

```
planqd tx distribution withdraw-rewards plqvaloper1tsrnkfqelmz4ggu2pygdxww3hupqv9x4mdjlar --from=plq1tsrnkfqelmz4ggu2pygdxww3hupqv9x49nl9mj --commission --chain-id=$PLANQ_CHAIN_ID --gas="1000000" --gas-prices="30000000000aplanq" --gas-adjustment="1.15"
```

```
planqd tx staking delegate $(planqd keys show wallet --bech val -a) 270000000000000000aplanq --gas="1000000" --gas-prices="30000000000aplanq" --gas-adjustment="1.15" --chain-id=planq_7070-2 --from plq1tsrnkfqelmz4ggu2pygdxww3hupqv9x49nl9mj
```
