<img src="https://github.com/romanr95/GUIDS/blob/main/NOLUS/LOGO_NOLUS.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://nolus.io/ <br>
```TWITTER``` - https://twitter.com/nolusprotocol <br>
```DISCORD``` - https://discord.gg/nolus-protocol <br>
```GITHUB``` - https://github.com/nolus-protocol <br>
```TELEGRAM``` - https://t.me/NolusProtocol <br>
```MEDIUM``` - https://medium.com/nolusprotocol <br>
```REDDIT``` - https://www.reddit.com/r/NolusProtocol/
# SERVICES
```RPC``` - 65.108.199.120:35457 <br>
```API``` - 65.108.199.120:1521 <br>
```PEER``` - 3c2e6d8aac25e2d620deb1478a02127aa8f7b346@65.108.199.120:35456 
# EXPLORERS
```NODES.GURU``` - https://nolus.explorers.guru/
# SOFTWARE REQUIREMENTS
Prerequisite: go1.19+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/nolus-protocol/nolus-core
cd nolus-core
git checkout v0.3.0
make install
```
# INIT THE CONFIG FILES
```
nolusd init <moniker> --chain-id pirin-1
nolusd config chain-id pirin-1
```
# CREATE OR RESTORE A WALLET
```
nolusd keys add <wallet_name>
nolusd keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
wget -O $HOME/.nolus/config/genesis.json "https://github.com/nolus-protocol/nolus-networks/blob/main/mainnet/pirin-1/genesis.json"
```
# ADD PEERS AND SEEDS
```
SEEDS=""
PEERS="39b1945d0ec6545ec9d45d0de9a9e7c058410b86@65.108.10.49:26457,1c6a4522b6f0f5217333032849f4a1dcfbbee218@38.242.134.110:26656,67d569007da736396d7b636224b97349adcde12f@51.89.98.102:55666,7740f125a480d1329fa1015e7ea97f09ee4eded7@107.135.15.66:26746,18845b356886a99ee704f7a06de79fc8208b47d1@57.128.96.155:19756"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.nolus/config/config.toml
```
# ADD MIN GAS
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025unls\"/" $HOME/.nolus/config/app.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/nolusd.service > /dev/null <<EOF
[Unit]
Description=Nolus
After=network-online.target

[Service]
User=$USER
ExecStart=$(which nolusd) start
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable nolusd
sudo systemctl restart nolusd && journalctl -fu nolusd -o cat
```
# CREATE VALIDATOR
```
nolusd tx staking create-validator \
  --amount=1000000unls \
  --pubkey=$(nolusd tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="pirin-1" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --gas="202000" \
  --fees="510unls" \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC=65.108.199.120:35457 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop nolusd && nolusd tendermint unsafe-reset-all --home $HOME/.nolus --keep-addr-book
```
```
peers="3c2e6d8aac25e2d620deb1478a02127aa8f7b346@65.108.199.120:35456"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nolus/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.nolus/config/config.toml
```
```
sudo systemctl restart nolusd && journalctl -fu nolusd -o cat
```
