<img src="https://github.com/romanr95/GUIDS/blob/main/UNUNIFI/LOGO_UNUNIFI.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://ununifi.io/ <br>
```TWITTER``` - https://twitter.com/ununifi/ <br>
```DISCORD``` - https://discord.gg/aAnyvcE82s <br>
```GITHUB``` - https://github.com/UnUniFi/ <br>
```CREW3``` - https://ununifi.crew3.xyz/ <br>
```MEDIUM``` - https://medium.com/@ununifi <br>
```WHITEPAPER``` - https://ununifi.io/assets/download/UnUniFi-Whitepaper.pdf <br>
```ROADMAP``` - https://cauchye.notion.site/2e949743f1ec438f8e2e2c57f824605b?v=58a2554bbb634fda887202f0562bbfa6 <br>
```GITBOOK``` - https://ununifi.gitbook.io/ <br>
```YOUTUBE``` - https://www.youtube.com/c/UnUniFi
# SERVICES
```RPC``` - 65.108.199.120:60657 <br>
```API``` - 65.108.199.120:1367 <br>
```PEER``` - e9539642f4ca58bb6dc09257d4ba8fc00467235f@65.108.199.120:60656 
# EXPLORERS
```UNUNIFI``` - https://ununifi.io/explorer/
# HARDWARE REQUIREMENTS
```MEMORY``` - 32GB <br>
```CPUs``` - 16 <br>
```DISK``` - 500GB
# SOFTWARE REQUIREMENTS
Prerequisite: go1.17+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/UnUniFi/chain ununifi
cd ununifi && git checkout v1.0.0-beta.4
make install
```
# INIT THE CONFIG FILES
```
ununifid init <moniker> --chain-id ununifi-beta-v1
```
# CREATE OR RESTORE A WALLET
```
ununifid keys add <wallet_name>
ununifid keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
curl https://raw.githubusercontent.com/UnUniFi/network/main/launch/ununifi-beta-v1/genesis.json > ~/.ununifi/config/genesis.json
```
# DOWNLOAD ADDRBOOK
```
curl https://anode.team/UnUniFi/main/addrbook.json > ~/.ununifi/config/addrbook.json
```
# ADD PEERS AND SEED
```
seeds="fa38d2a851de43d34d9602956cd907eb3942ae89@a.ununifi.cauchye.net:26656,404ea79bd31b1734caacced7a057d78ae5b60348@b.ununifi.cauchye.net:26656,1357ac5cd92b215b05253b25d78cf485dd899d55@[2600:1f1c:534:8f02:7bf:6b31:3702:2265]:26656,25006d6b85daeac2234bcb94dafaa73861b43ee3@[2600:1f1c:534:8f02:a407:b1c6:e8f5:94b]:26656,caf792ed396dd7e737574a030ae8eabe19ecdf5c@[2600:1f1c:534:8f02:b0a4:dbf6:e50b:d64e]:26656,796c62bb2af411c140cf24ddc409dff76d9d61cf@[2600:1f1c:534:8f02:ca0e:14e9:8e60:989e]:26656,cea8d05b6e01188cf6481c55b7d1bc2f31de0eed@[2600:1f1c:534:8f02:ba43:1f69:e23a:df6b]:26656"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$seeds\"/; s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.ununifi/config/config.toml
```
# ADD MIN GAS
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025uguu\"/" $HOME/.ununifi/config/app.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/ununifid.service > /dev/null <<EOF
[Unit]
Description=UnUniFid Daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which ununifid) start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable ununifid
sudo systemctl restart ununifid && journalctl -fu ununifid -o cat
```
# CREATE VALIDATOR
```
ununifid tx staking create-validator \
  --amount=1000000uguu \
  --pubkey=$(ununifid tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="ununifi-beta-v1" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.1" \
  --min-self-delegation="1" \
  --gas-prices 0.025uguu \
  --node "tcp://127.0.0.1:26657" \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC=65.108.199.120:60657 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop ununifid && ununifid unsafe-reset-all --home $HOME/.ununifi
```
```
peers="e9539642f4ca58bb6dc09257d4ba8fc00467235f@65.108.199.120:60656"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.ununifi/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.ununifi/config/config.toml
```
```
sudo systemctl restart ununifid && journalctl -u ununifid -f
```
