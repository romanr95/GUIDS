<img src="https://github.com/romanr95/GUIDS/blob/main/TERITORI/TERITORI%20LOGO.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://teritori.com/ <br>
```TWITTER``` - https://twitter.com/TeritoriNetwork/ <br>
```DISCORD``` - https://discord.gg/teritori/ <br>
```GITHUB``` - https://github.com/TERITORI/ <br>
```CREW3``` - https://teritori.crew3.xyz/ <br>
```MEDIUM``` - https://medium.com/teritori/ <br>
```WHITEPAPER``` - https://teritori.gitbook.io/teritori-whitepaper/ <br>
```ROADMAP``` - https://teritori.com/roadmap/ <br>
```THE ALPHA dApp``` - https://app.teritori.com/ <br>
```COINGECKO``` - https://www.coingecko.com/en/coins/teritori/
# SERVICES
```RPC``` - 65.108.199.120:36657 <br>
```API``` - 65.108.199.120:1327 <br>
```PEER``` - fcd6ccd5fef149059fa5d1696b3b358889046f3a@65.108.199.120:36656 
# EXPLORERS
```MINTSCAN``` - https://www.mintscan.io/teritori/ <br>
```PINGPUB``` - https://ping.pub/teritori/ <br>
```ATOMSCAN``` - https://atomscan.com/teritori/
# HARDWARE REQUIREMENTS
```MEMORY``` - 32GB <br>
```CPUs``` - 16 <br>
```DISK``` - 500GB
# SOFTWARE REQUIREMENTS
Prerequisite: go1.18+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/TERITORI/teritori-chain
cd teritori-chain && git checkout v1.3.0
make install
```
# INIT THE CONFIG FILES
```
teritorid init <moniker> --chain-id teritori-1
teritorid config chain-id teritori-1
```
# CREATE OR RESTORE A WALLET
```
teritorid keys add <wallet_name>
teritorid keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
wget -O $HOME/.teritorid/config/genesis.json "https://media.githubusercontent.com/media/TERITORI/teritori-chain/v1.1.2/mainnet/teritori-1/genesis.json"
```
# DOWNLOAD ADDRBOOK
```
wget -O $HOME/.teritorid/config/addrbook.json "http://65.108.6.45:8000/teritori/addrbook.json"
```
# ADD PEERS
```
peers="3069b058b5ed85c3cdb2cf18fb1d255d966b53af@193.149.187.8:26656,a06fbbb9ace823ae28a696a91daa2d0644653c28@65.21.32.200:26756"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$seeds\"/; s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.teritorid/config/config.toml
```
# ADD MIN GAS
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.25unibi\"/" $HOME/.nibid/config/app.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/teritorid.service > /dev/null <<EOF
[Unit]
Description=Teritori Daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which teritorid) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable teritorid
sudo systemctl restart teritorid && journalctl -fu teritorid -o cat
```
# CREATE VALIDATOR
```
teritorid tx staking create-validator \
  --amount=1000000utori \
  --pubkey=$(teritorid tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="teritori-1" \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1000000" \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC=65.108.199.120:36657 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop teritorid && teritorid tendermint unsafe-reset-all --home $HOME/.teritorid --keep-addr-book
```
```
peers="fcd6ccd5fef149059fa5d1696b3b358889046f3a@65.108.199.120:36656"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.teritorid/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.teritorid/config/config.toml
```
```
curl -o - -L https://anode.team/Teritori/main/anode.team_teritori_wasm.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.teritorid/data
```
```
sudo systemctl restart teritorid && journalctl -fu teritorid -o cat
```
