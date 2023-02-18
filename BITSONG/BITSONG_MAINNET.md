<img src="https://github.com/romanr95/GUIDS/blob/main/BITSONG/BITSONG_LOGO.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://bitsong.io/ <br>
```TWITTER``` - https://twitter.com/bitsongofficial <br>
```DISCORD``` - https://discord.gg/XRByJSEeHc <br>
```TELEGRAM``` - https://t.me/BitSongOfficial <br>
```FACEBOOK``` - https://www.facebook.com/bitsongofficial <br>
```REDDIT``` https://www.reddit.com/r/bitsong <br>
```GITHUB``` - https://github.com/bitsongofficial <br>
```LINKEDIN``` - https://www.linkedin.com/company/bitsong/ <br>
```CREW3``` - https://crew3.xyz/c/bitsongofficial/ <br>
```MEDIUM``` - https://bitsongofficial.medium.com/ <br>
```DOCUMENTATIONS``` - https://docs.bitsong.io/ <br>
```WALLET``` - https://wallet.bitsong.io/ <br>
```SINFONIA (TESTNET)``` - https://testnet.sinfonia.zone/ <br>
```SINFONIA APP``` - https://app.sinfonia.zone/ <br>
```COINGECKO``` - https://www.coingecko.com/en/coins/bitsong
# SERVICES
```RPC``` - 65.108.199.120:26657 <br>
```API``` - 65.108.199.120:1317 <br>
```PEER``` - 08522d08679779293a1d4a1ea1e28738512274a0@65.108.199.120:26656 
# EXPLORERS
```MINTSCAN``` - https://www.mintscan.io/bitsong <br>
```PINGPUB``` - https://ping.pub/bitsong <br>
```ATOMSCAN``` - https://atomscan.com/bitsong <br>
```BIG DIPPER``` - https://bitsong.bigdipper.live/
# HARDWARE REQUIREMENTS
```MEMORY``` - 32GB <br>
```CPUs``` - 16 <br>
```DISK``` - 500GB
# SOFTWARE REQUIREMENTS
Prerequisite: go1.19+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/bitsongofficial/go-bitsong
cd go-bitsong && git checkout v0.14.0
make install
```
# INIT THE CONFIG FILES
```
bitsongd init <moniker> --chain-id bitsong-2b
bitsongd config chain-id bitsong-2b
```
# CREATE OR RESTORE A WALLET
```
bitsongd keys add <wallet_name>
bitsongd keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
curl https://anode.team/BitSong/main/genesis.json > ~/.bitsongd/config/genesis.json
```
# DOWNLOAD ADDRBOOK
```
curl https://anode.team/BitSong/main/addrbook.json > ~/.bitsongd/config/addrbook.json
```
# ADD PEERS
```
SEEDS=""
PEERS="e2b9971222adf71f7199c670b7e85471c447e926@157.90.255.143:26656,120740c15a8a19c232b1aa4d80b20de248b33db3@135.181.129.94:26656,d741773bc5eecbefb7b14fcca5e3e0fedd49d5a3@157.90.95.104:26656,6e93a30587671e2cecacbcbb27092809bb20249f@95.217.203.59:31656,adfe1cf240780cf8d58266171ced72fb4e9a7a6d@23.226.14.168:26656,f36d3a926ae0583e60f00e7bc54711f3cb7fe769@195.201.58.166:26656,9c9f030298bdda9ca69de7db8e9a3aef33972fba@135.181.16.236:31656,9806602afb65ba45d1048d65285d5c6e50285088@178.18.242.242:26656,4fdd438ea70927003022ecc308e36bc1924ec598@51.210.104.207:26656,3cf3effd3ecb33bdbb5c5e6528c88fde4869b97c@116.202.139.113:26656,075cf589e44c74687ef3a4df3a583f482bce57e0@46.166.143.79:26656,f9d318eaf38988ce2b65b795068d86b214866c91@141.94.170.26:26256,fa932748b327fdde6d235b28a9850f8b8bd3326a@95.217.119.101:31656,d52f6e4fe1819133474e977d7e1d73124d1f4af5@95.217.156.76:26656,5ebab02914638005773dac8026f441e06c115a44@74.207.226.176:26656,e5428ce29ccd26434828a577906ac9c413ca6a48@80.71.57.42:26656,2afc435e2246ff3f16ade85b52264367945d12b5@176.58.124.226:26656"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.bitsongd/config/config.toml
```
# ADD MIN GAS
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.001ubtsg\"/" $HOME/.bitsongd/config/app.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/bitsongd.service > /dev/null <<EOF
[Unit]
Description=BitSong
After=network-online.target

[Service]
User=$USER
ExecStart=$(which bitsongd) start
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable bitsongd
sudo systemctl restart bitsongd && journalctl -fu bitsongd -o cat
```
# CREATE VALIDATOR
```
bitsongd tx staking create-validator \
  --amount=1000000ubtsg \
  --pubkey=$(bitsongd tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="bitsong-2b" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --fees="500ubtsg" \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC=65.108.199.120:26657 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop bitsongd && bitsongd tendermint unsafe-reset-all --home $HOME/.bitsongd --keep-addr-book
```
```
peers="08522d08679779293a1d4a1ea1e28738512274a0@65.108.199.120:26656"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.bitsongd/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.bitsongd/config/config.toml
```
```
curl -o - -L https://anode.team/BitSong/main/anode.team_bitsong_wasm.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.bitsongd/data
```
```
sudo systemctl restart bitsongd && journalctl -fu bitsongd -o cat
```
