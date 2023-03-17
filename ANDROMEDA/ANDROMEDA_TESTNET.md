<img src="https://github.com/romanr95/GUIDS/blob/main/ANDROMEDA/LOGO%20ANDROMEDA.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://andromedaprotocol.io/ <br>
```TWITTER``` - https://twitter.com/AndromedaProt/ <br>
```DISCORD``` - https://discord.gg/GBd6buKYyZ <br>
```GITHUB``` - https://github.com/andromedaprotocol/ <br>
```MEDIUM``` - https://medium.com/andromeda-engineering/ <br>
```TELEGRAM``` - https://t.me/andromedaprotocol/ <br>
```WHITEPAPER``` - https://static1.squarespace.com/static/60b85587d5bf80784bda317f/t/61698246efa74863509bacd8/1634304583185/ANDROMEDA+PROTOCOL.pdf
# SERVICES
```RPC``` - 65.108.199.120:61357 <br>
```API``` - 65.108.199.120:1497 <br>
```PEER``` - 50ca8e25cf1c5a83aa4c79bb1eabfe88b20eb367@65.108.199.120:61356 
# EXPLORERS
```WILDSAGE``` - https://testnet-ping.wildsage.io/andromeda/
# HARDWARE REQUIREMENTS
```MEMORY``` - 32GB <br>
```CPUs``` - 16 <br>
```DISK``` - 500GB
# SOFTWARE REQUIREMENTS
Prerequisite: go1.18+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/andromedaprotocol/andromedad.git
cd andromedad && git checkout galileo-3-v1.1.0-beta1
make install
```
# INIT THE CONFIG FILES
```
andromedad init <moniker> --chain-id galileo-3
andromedad config chain-id galileo-3
```
# CREATE OR RESTORE A WALLET
```
andromedad keys add <wallet_name>
andromedad keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
wget -qO $HOME/.andromeda/config/genesis.json wget "https://snapshot.yeksin.net/andromeda/genesis.json"
```
# DOWNLOAD ADDRBOOK
```
wget -qO $HOME/.andromedad/config/addrbook.json wget "https://snapshot.yeksin.net/andromeda/addrbook.json"
```
# ADD PEERS
```
PEERS="06d4ab2369406136c00a839efc30ea5df9acaf11@10.128.0.44:26656,43d667323445c8f4d450d5d5352f499fa04839a8@192.168.0.237:26656,29a9c5bfb54343d25c89d7119fade8b18201c503@192.168.101.79:26656,6006190d5a3a9686bbcce26abc79c7f3f868f43a@37.252.184.230:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.andromedad/config/config.toml
```
# ADD MIN GAS
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.25uandr\"/" $HOME/.andromedad/config/app.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/andromedad.service > /dev/null <<EOF
[Unit]
Description= Andromeda Network Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which andromedad) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable andromedad
sudo systemctl restart andromedad && journalctl -fu andromedad -o cat
```
# CREATE VALIDATOR
```
andromedad tx staking create-validator \
  --amount=1000000uandr \
  --pubkey=$(andromedad tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="galileo-3" \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1000000" \
  --fees=2000uandr \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC=65.108.199.120:61357 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop andromedad && andromedad tendermint unsafe-reset-all --home $HOME/.andromedad --keep-addr-book
```
```
peers="50ca8e25cf1c5a83aa4c79bb1eabfe88b20eb367@65.108.199.120:61356"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.andromedad/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.andromedad/config/config.toml
```
```
sudo systemctl restart andromedad && journalctl -fu andromedad -o cat
```
