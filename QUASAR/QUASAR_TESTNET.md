<img src="https://github.com/romanr95/GUIDS/blob/main/QUASAR/LOGO_QUASAR.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://www.quasar.fi/ <br>
```TWITTER``` - https://twitter.com/QuasarFi <br>
```DISCORD``` - https://discord.gg/quasarfi <br>
```GITHUB``` - https://github.com/quasar-finance <br>
```CREW3``` - https://crew3.xyz/c/quasar/ <br>
```MEDIUM``` - https://medium.com/@quasar.fi <br>
```DOCUMENTATION``` - https://docs.quasar.fi/ 
# SERVICES
```RPC``` - 65.108.199.120:61057 <br>
```API``` - 65.108.199.120:1307 <br>
```PEER``` - 9c5e550669e4668e536ae3b2fb15a3e6c226fa18@65.108.199.120:61056 
# EXPLORERS
```ITROCKET``` - https://testnet.itrocket.net/quasar/
# HARDWARE REQUIREMENTS
```MEMORY``` - 16GB <br>
```CPUs``` - 4 <br>
```DISK``` - 500GB
# SOFTWARE REQUIREMENTS
Prerequisite: go1.19+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
wget https://github.com/quasar-finance/binary-release/raw/main/v0.0.2-alpha-11/quasarnoded-linux-amd64
chmod +x quasarnoded-linux-amd64
sudo mv quasarnoded-linux-amd64 $HOME/go/bin/quasarnoded
```
# INIT THE CONFIG FILES
```
quasarnoded init <moniker> --chain-id qsr-questnet-04
quasarnoded config chain-id qsr-questnet-04
```
# CREATE OR RESTORE A WALLET
```
quasarnoded keys add <wallet_name>
quasarnoded keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
wget -O $HOME/.quasarnode/config/genesis.json https://files.itrocket.net/testnet/quasar/genesis.json
```
# ADD PEERS
```
SEEDS=""
PEERS="6ccfdbe91c06698f0a66cf95a249dbcd88b5aaa4@quasar-testnet-peer.itrocket.net:443,bba6e85e3d1f1d9c127324e71a982ddd86af9a99@88.99.3.158:18256,bcb8d2b5d5464cddbab9ce2705aae3ad3e38aeac@144.76.67.53:2490,1c1043ae487c91209fce8c589a5772a7f3846e7c@136.243.88.91:8080,1112acc7479a8a1afb0777b0b9a39fb1f7e77abd@34.175.69.87:26656,bffb10a5619be7bfa98919e08f4a6bef4f8f6bf0@135.181.210.186:26656,695b9dc49a979e4c5986c5ae9a6effc8bc8597f0@185.197.250.151:27656,8937bdacf1f0c8b2d1ffb4606554eaf08bd55df4@5.75.255.107:26656,a23f002bda10cb90fa441a9f2435802b35164441@38.146.3.203:18256,41ee7632f310c035235828ce03c208dbe1e24d7d@38.146.3.204:18256,966acc999443bae0857604a9fce426b5e09a7409@65.108.105.48:18256"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.quasarnode/config/config.toml
```
# ADD MIN GAS
```
sed -i 's/minimum-gas-prices =.*/minimum-gas-prices = "0.0uqsr"/g' $HOME/.quasarnode/config/app.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/quasarnoded.service > /dev/null <<EOF
[Unit]
Description=quasar
After=network-online.target

[Service]
User=$USER
ExecStart=$(which quasarnoded) start --home $HOME/.quasarnode
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo sudo systemctl enable quasarnoded
sudo systemctl restart quasarnoded && sudo journalctl -u quasarnoded -f
```
# CREATE VALIDATOR
```
quasarnoded tx staking create-validator \
  --amount=1000000uqsr \
  --pubkey=$(quasarnoded tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="qsr-questnet-04" \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1000000" \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC=65.108.199.120:61057 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop quasarnoded && quasarnoded tendermint unsafe-reset-all --home $HOME/.quasarnode --keep-addr-book
```
```
peers="9c5e550669e4668e536ae3b2fb15a3e6c226fa18@65.108.199.120:61056"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.quasarnode/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.quasarnode/config/config.toml
```
```
sudo systemctl restart quasarnoded && journalctl -fu quasarnoded -o cat
```

