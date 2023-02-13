<img src="https://github.com/romanr95/GUIDS/blob/main/HAQQ/HAQQ_LOGO.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://islamiccoin.net/ <br>
```TWITTER``` - https://twitter.com/Islamic_coin <br>
```DISCORD``` - https://discord.gg/islamic-coin <br>
```GITHUB``` - https://github.com/haqq-network <br>
```CREW3``` - https://crew3.xyz/c/haqq-val-contest/ <br>
```MEDIUM``` - https://blog.islamiccoin.net/ <br>
```WHITEPAPER``` - https://islamiccoin.net/wp <br>
```YOUTUBE``` - https://www.youtube.com/channel/UCQqtQStp2Ba-PYIKnuf_Yeg <br>
```LINKEDIN``` - https://www.linkedin.com/company/islamiccoin <br>
```TELEGRAM``` - https://t.me/islamiccoin_community <br>
```DOCUMENTATION``` - https://docs.haqq.network/
# SERVICES
```RPC``` - 65.108.199.120:56657 <br>
```API``` - 65.108.199.120:1347 <br>
```PEER``` - 19f1039614af2808abc97d959d374cdca982a109@65.108.199.120:56656 
# EXPLORERS
```HAQQ``` - https://explorer.haqq.network/ 
# HARDWARE REQUIREMENTS
```MEMORY``` - 32GB <br>
```CPUs``` - 4 <br>
```DISK``` - 500GB
# SOFTWARE REQUIREMENTS
Prerequisite: go1.18+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/haqq-network/haqq
cd haqq && git checkout v1.3.1
make install
```
# INIT THE CONFIG FILES
```
haqqd init <moniker> --chain-id haqq_54211-3
haqqd config chain-id haqq_54211-3
```
# CREATE OR RESTORE A WALLET
```
haqqd keys add <wallet_name>
haqqd keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
wget -O $HOME/.haqqd/config/genesis.json "https://github.com/haqq-network/validators-contest/raw/master/genesis.json"
```
# DOWNLOAD ADDRBOOK
```
wget -O $HOME/.haqqd/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/haqq/addrbook.json"
```
# ADD MIN GAS
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025aISLM\"/;" ~/.haqqd/config/app.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/haqqd.service > /dev/null <<EOF
[Unit]
Description=haqqd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which haqqd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable haqqd
sudo systemctl restart haqqd && sudo journalctl -u haqqd -f -o cat
```
# CREATE VALIDATOR
```
haqqd tx staking create-validator \
  --amount=1000000000000000000aISLM \
  --pubkey=$(haqqd tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="haqq_54211-3" \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1000000" \
  --fees 555aISLM \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC="65.108.199.120:56657" && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop haqqd && haqqd tendermint unsafe-reset-all --home $HOME/.haqqd
```
```
peers="19f1039614af2808abc97d959d374cdca982a109@65.108.199.120:56656" \ && 
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.haqqd/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.haqqd/config/config.toml
```
```
sudo systemctl restart haqqd && journalctl -fu haqqd -o cat
```
