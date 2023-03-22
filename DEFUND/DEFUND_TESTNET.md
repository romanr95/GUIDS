<img src="https://github.com/romanr95/GUIDS/blob/main/DEFUND/LOGO_DEFUND.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://www.defund.app/ <br>
```TWITTER``` - https://twitter.com/defund_finance <br>
```DISCORD``` - https://discord.gg/HFmQZn3K <br>
```MEDIUM``` - https://medium.com/defund-finance <br>
```GITHUB``` - https://github.com/defund-labs
# SERVICES
```RPC``` - 65.108.199.120:23157 <br>
```API``` - 65.108.199.120:1713 <br>
```PEER``` - 869173cd0f63a756010b6077e7e6cc03c56a1dcc@65.108.199.120:23156
# HARDWARE REQUIREMENTS
```MEMORY``` - 32GB <br>
```CPUs``` - 16 <br>
```DISK``` - 500GB
# SOFTWARE REQUIREMENTS
Prerequisite: go1.19+ required. ref <br>
Prerequisite: git. ref
# INSTALL LAST BINARY
```
git clone https://github.com/defund-labs/defund.git
cd defund
git checkout v0.2.6
make install
```
# INIT THE CONFIG FILES
```
defundd init <moniker> --chain-id orbit-alpha-1
defundd config chain-id orbit-alpha-1
```
# CREATE OR RESTORE A WALLET
```
defundd keys add <wallet_name>
defundd keys add <wallet_name> --recover
```
# DOWNLOAD GENESIS
```
curl https://anode.team/DeFund/test/genesis.json > ~/.defund/config/genesis.json
```
# DOWNLOAD ADDRBOOK
```
curl https://anode.team/DeFund/test/addrbook.json > ~/.defund/config/addrbook.json
```
# ADD PEERS, SEED
```
SEEDS="f902d7562b7687000334369c491654e176afd26d@170.187.157.19:26656,2b76e96658f5e5a5130bc96d63f016073579b72d@rpc-1.defund.nodes.guru:45656"
PEERS="f902d7562b7687000334369c491654e176afd26d@170.187.157.19:26656,f8093378e2e5e8fc313f9285e96e70a11e4b58d5@rpc-2.defund.nodes.guru:45656,878c7b70a38f041d49928dc02418619f85eecbf6@rpc-3.defund.nodes.guru:45656,3594b1f46c6321d9f99cda8ad5ef5a367ce06ccf@199.247.16.116:26656"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.defund/config/config.toml
```
# OPTIMIZE DEFUND
```
bash $HOME/defund/devtools/optimize.sh
```
# ADD MIN GAS
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ufetf\"/" $HOME/.defund/config/app.toml
```
# CREATE THE SERVICE FILE
```
sudo tee /etc/systemd/system/defundd.service > /dev/null <<EOF
[Unit]
Description=DeFund Test
After=network-online.target

[Service]
User=$USER
ExecStart=$(which defundd) start
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```
# LOAD SERVICE AND START
```
sudo systemctl daemon-reload && sudo systemctl enable defundd
sudo systemctl restart defundd && journalctl -fu defundd -o cat
```
# CREATE VALIDATOR
```
defundd tx staking create-validator \
  --amount=1000000ufetf \
  --pubkey=$(defundd tendermint show-validator) \
  --moniker="<moniker>" \
  --identity="<identity>" \
  --website="<website>" \
  --details="<details>" \
  --security-contact="<contact>" \
  --chain-id="orbit-alpha-1" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --fees="0ufetf" \
  --from=<wallet_name>
  ```
  # STATE-SYNC
```
SNAP_RPC=65.108.199.120:23157 && \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```
```
sudo systemctl stop defundd && defundd tendermint unsafe-reset-all --home $HOME/.defund --keep-addr-book
```
```
peers="869173cd0f63a756010b6077e7e6cc03c56a1dcc@65.108.199.120:23156"
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.defund/config/config.toml
```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.defund/config/config.toml
```
```
curl -o - -L https://anode.team/DeFund/test/anode.team_defund_wasm.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.defund/
```
```
sudo systemctl restart defundd && journalctl -fu defundd -o cat
```
