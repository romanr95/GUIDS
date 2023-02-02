<img src="https://github.com/romanr95/GUIDS/blob/main/NIBIRU%20LOGO.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://nibiru.fi/ <br>
```TWITTER``` - https://twitter.com/NibiruChain/ <br>
```DISCORD``` - https://discord.gg/nibiru/ <br>
```GITHUB``` - https://github.com/NibiruChain/ <br>
```GITBOOK``` - https://docs.nibiru.fi/ <br>
```MEDIUM``` - https://blog.nibiru.fi/ <br>
```LINKEDIN``` - https://www.linkedin.com/company/nibiruchain/ 
# SERVICES
```RPC``` - 65.108.199.120:36657 <br>
```API``` - 65.108.199.120:1327 <br>
```PEER``` - fcd6ccd5fef149059fa5d1696b3b358889046f3a@65.108.199.120:36656 
# EXPLORERS
```NIBIRU.FI``` - https://testnet-2.nibiru.fi/ 
# SOFTWARE REQUIREMENTS
Prerequisite: go1.18.5+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/NibiruChain/nibiru
cd nibiru
git checkout v0.16.3
make install
```
# INIT THE CONFIG FILES
```
nibid init <moniker> --chain-id nibiru-testnet-2
nibid config chain-id nibiru-testnet-2
```
# CREATE OR RESTORE A WALLET
```
nibid keys add <wallet_name>
nibid keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
curl https://anode.team/Nibiru/test/genesis.json > ~/.nibid/config/genesis.json
```
# DOWNLOAD ADDRBOOK
```
curl https://anode.team/Nibiru/test/addrbook.json > ~/.nibid/config/addrbook.json
```
# ADD PEERS
```
peers="5d9432668a2acd0587ecb77b5728177d216c02bc@65.109.93.152:36316"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$seeds\"/; s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.nibid/config/config.toml
```
# ADD MIN GAS
```
sed -i 's/minimum-gas-prices = ""/minimum-gas-prices = "0.25unibi"/g' ~/.nibid/config/app.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/nibid.service > /dev/null <<EOF
[Unit]
Description=Nibiru
After=network-online.target

[Service]
User=$USER
ExecStart=$(which nibid) start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable nibid
sudo systemctl restart nibid && journalctl -fu nibid -o cat
```
# CREATE VALIDATOR
```
nibid tx staking create-validator \
  --amount=10000000unibi \
  --pubkey=$(nibid tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="nibiru-testnet-2" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --fees 5000unibi \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC=https://nibiru.rpc.t.anode.team:443 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop nibid && nibid tendermint unsafe-reset-all --home $HOME/.nibid --keep-addr-book
```
```
peers="5d9432668a2acd0587ecb77b5728177d216c02bc@65.109.93.152:36317"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nibid/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.nibid/config/config.toml
```
```
curl -o - -L https://anode.team/Nibiru/test/anode.team_nibiru_wasm.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.nibid/data
```
```
sudo systemctl restart nibid && journalctl -fu nibid -o cat
```
