<img src="https://github.com/romanr95/GUIDS/blob/main/LAVA/LOGO_LAVA.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://lavanet.xyz/ <br>
```TWITTER``` - https://twitter.com/lavanetxyz <br>
```DISCORD``` - https://discord.gg/lavanetxyz <br>
```GITHUB``` - https://github.com/lavanet/ <br>
```MEDIUM``` - https://medium.com/lava-network <br>
```LITEPAPER``` - https://lavanet.xyz/assets/lava_litepaper_v0_1.pdf <br>
```DOCUMENTATIONS``` - https://docs.lavanet.xyz/
# SERVICES
```RPC``` - 65.108.199.120:60757 <br>
```API``` - 65.108.199.120:1377 <br>
```PEER``` - e5f324d671e8bba44cd8eef2cb5b6e46ccf4f95a@65.108.199.120:60756 
# EXPLORERS
```ITROCKET``` - https://testnet.itrocket.net/lava/ 
# HARDWARE REQUIREMENTS
```MEMORY``` - 32GB <br>
```CPUs``` - 16 <br>
```DISK``` - 500GB
# SOFTWARE REQUIREMENTS
Prerequisite: go1.18+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/lavanet/lava
cd lava && git checkout v0.6.0
make install
```
# INIT THE CONFIG FILES
```
lavad init <moniker> --chain-id lava-testnet-1
lavad config chain-id lava-testnet-1
```
# CREATE OR RESTORE A WALLET
```
lavad keys add <wallet_name>
lavad keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
curl https://anode.team/Lava/test/genesis.json > ~/.lava/config/genesis.json
```
# DOWNLOAD ADDRBOOK
```
curl https://anode.team/Lava/test/addrbook.json > ~/.lava/config/addrbook.json
```
# ADD PEERS AND SEED
```
seeds="3a445bfdbe2d0c8ee82461633aa3af31bc2b4dc0@prod-pnet-seed-node.lavanet.xyz:26656,e593c7a9ca61f5616119d6beb5bd8ef5dd28d62d@prod-pnet-seed-node2.lavanet.xyz:26656"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$seeds\"/; s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.lava/config/config.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/lavad.service > /dev/null <<EOF
[Unit]
Description=Lava
After=network-online.target

[Service]
User=$USER
ExecStart=$(which lavad) start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable lavad
sudo systemctl restart lavad && journalctl -fu lavad -o cat
```
# CREATE VALIDATOR
```
lavad tx staking create-validator \
  --amount=1000000000ulava \
  --pubkey=$(lavad tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="lava-testnet-1" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --fees="200ulava" \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC=65.108.199.120:60757 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop lavad && lavad tendermint unsafe-reset-all --home $HOME/.lava --keep-addr-book
```
```
peers="e5f324d671e8bba44cd8eef2cb5b6e46ccf4f95a@65.108.199.120:60756"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.lava/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.lava/config/config.toml
```
```
sudo systemctl restart lavad && journalctl -u lavad -f
```
