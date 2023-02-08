<img src="https://github.com/romanr95/GUIDS/blob/main/HUMANS/LOGO_HUMANS.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://humans.ai/ <br>
```TWITTER``` - https://twitter.com/humansdotai <br>
```DISCORD``` - https://discord.gg/humansdotai <br>
```GITHUB``` - https://github.com/humansdotai/ <br>
```INSTAGRAM``` - https://www.instagram.com/humansdotai <br>
```MEDIUM``` - https://medium.com/humansdotai <br>
```DOCUMENTATION``` - https://docs.humans.zone/ <br>
```YOUTUBE``` - https://www.youtube.com/channel/UCRPyI284yy0FWWz9YEWx2XQ
# SERVICES
```RPC``` - 65.108.199.120:51656 <br>
```API``` - 65.108.199.120:1357 <br>
```PEER``` - 6fad1e09897a9c5be8bc07ba34d48e8eb1414edf@65.108.199.120:57656
# EXPLORERS
```HUMANS``` - https://explorer.humans.zone/ <br>
# HARDWARE REQUIREMENTS
```MEMORY``` - 32GB <br>
```CPUs``` - 16 <br>
```DISK``` - 500GB
# SOFTWARE REQUIREMENTS
Prerequisite: go1.19+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/humansdotai/humans
cd humans && git checkout v1
go build -o humansd cmd/humansd/main.go
mv humansd /root/go/bin/humansd
```
# INIT THE CONFIG FILES
```
humansd init <moniker> --chain-id testnet-1
humansd config chain-id testnet-1
```
# CREATE OR RESTORE A WALLET
```
humansd keys add <wallet_name>
humansd keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
curl https://anode.team/Humans/test/genesis.json > ~/.humans/config/genesis.json
```
# DOWNLOAD ADDRBOOK
```
curl https://anode.team/Humans/test/addrbook.json > ~/.humans/config/addrbook.json
```
# ADD PEERS AND SEED
```
peers="1df6735ac39c8f07ae5db31923a0d38ec6d1372b@45.136.40.6:26656,9726b7ba17ee87006055a9b7a45293bfd7b7f0fc@45.136.40.16:26656,6e84cde074d4af8a9df59d125db3bf8d6722a787@45.136.40.18:26656,eda3e2255f3c88f97673d61d6f37b243de34e9d9@45.136.40.13:26656,4de8c8acccecc8e0bed4a218c2ef235ab68b5cf2@45.136.40.12:26656"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$seeds\"/; s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.humans/config/config.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/humansd.service > /dev/null <<EOF
[Unit]
Description=Humans
After=network-online.target

[Service]
User=$USER
ExecStart=$(which humansd) start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable humansd
sudo systemctl restart humansd && journalctl -fu humansd -o cat
```
# CREATE VALIDATOR
```
humansd tx staking create-validator \
  --amount=10000000uheart \
  --pubkey=$(humansd tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="testnet-1" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --from=<wallet_name>
```
# STATE-SYNC
```
SNAP_RPC=65.108.199.120:51656 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop humansd && humansd tendermint unsafe-reset-all --home $HOME/.humans --keep-addr-book
```
```
peers="6fad1e09897a9c5be8bc07ba34d48e8eb1414edf@65.108.199.120:57656"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.humans/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.humans/config/config.toml
```
```
sudo systemctl restart humansd && journalctl -u humansd -f
```
