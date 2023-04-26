<img src="https://github.com/romanr95/GUIDS/blob/main/CASCADIA/LOGO_CASCADIA.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://www.cascadia.foundation/ <br>
```TWITTER``` - https://twitter.com/CascadiaSystems <br>
```DISCORD``` - https://discord.gg/cascadia <br>
```GITHUB``` - https://github.com/cascadiafoundation <br>
```MEDIUM``` - https://medium.com/@CascadiaFoundation <br>
```TELEGRAM``` - https://t.me/CascadiaFoundation <br>
```TELEGRAM FOR VALIDATORS``` - https://t.me/+Tf6pQQSA7IkxNmU5 <br>
```WHITEPAPER``` - https://drive.google.com/file/d/1t_s9tc04Ewr_CzHEWochzwZzVL7Vh3bz/edit <br>
```ZEALY``` - https://zealy.io/c/cascadia/
# SERVICES
```RPC``` - 65.108.199.120:61357 <br>
```API``` - 65.108.199.120:1497 <br>
```PEER``` - 50ca8e25cf1c5a83aa4c79bb1eabfe88b20eb367@65.108.199.120:61356 
# EXPLORERS
```BLOCK``` - https://explorer.cascadia.foundation/ <br>
```VALIDATOR``` https://validator.cascadia.foundation/
# HARDWARE REQUIREMENTS
```MEMORY``` - 8GB <br>
```CPUs``` - 2 x dedicated/physical CPUs, either Intel or AMD, with the SSE4.1 and SSE4.2 flags (use lscpu to verify) <br>
```DISK``` - 200GB SSD
# SOFTWARE REQUIREMENTS
Prerequisite: go1.18.5+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/CascadiaFoundation/cascadia
cd cascadia
git checkout v0.1.1
make install
```
# INIT THE CONFIG FILES
```
cascadiad init <moniker> --chain-id cascadia_6102-1
cascadiad config chain-id cascadia_6102-1
```
# CREATE OR RESTORE A WALLET
```
cascadiad keys add <wallet_name> 
cascadiad keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
curl https://anode.team/Cascadia/test/genesis.json > ~/.cascadiad/config/genesis.json
```
# DOWNLOAD ADDRBOOK
```
curl https://anode.team/Cascadia/test/addrbook.json > ~/.cascadiad/config/addrbook.json
```
# ADD PEERS
```
SEEDS=""
PEERS="1d61222b7b8e180aacebfd57fbd2d8ab95ebdc4c@65.109.93.152:35656"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.cascadiad/config/config.toml
```
# ADD MIN GAS
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025aCC\"/" $HOME/.cascadiad/config/app.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/cascadiad.service > /dev/null <<EOF
[Unit]
Description=Cascadia Foundation
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cascadiad) start
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable cascadiad
sudo systemctl restart cascadiad && journalctl -fu cascadiad -o cat
```
# CREATE VALIDATOR
```
cascadiad tx staking create-validator \
  --amount=1000000000000000000aCC \
  --pubkey=$(cascadiad tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="cascadia_6102-1" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --gas "auto" \
  --gas-adjustment=1.2 \
  --gas-prices="7aCC" \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC=https://cascadia.rpc.t.anode.team:443 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 5000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
wget https://anode.team/unsafe-reset-all.sh && chmod u+x unsafe-reset-all.sh && ./unsafe-reset-all.sh cascadiad .cascadiad
```
```
peers="1d61222b7b8e180aacebfd57fbd2d8ab95ebdc4c@65.109.93.152:35656"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.cascadiad/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.cascadiad/config/config.toml
```
```
sudo systemctl restart cascadiad && journalctl -fu cascadiad -o cat
```
