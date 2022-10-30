<img src="https://github.com/romanr95/Guids/blob/main/TERITORI%20LOGO.png" width="1050" alt="" />

# TERITORI NETWORK (MAINNET) УСТАНОВКА НОДЫ И СОЗДАНИЕ ВАЛИДАТОРА
# Установка ноды
Устанавливаем бинарники
```bash
git clone https://github.com/TERITORI/teritori-chain && cd teritori-chain
git checkout v1.2.0
make install
```
Проверяем версию 
```bash
teritorid version --long | head
# version: v1.2.0
```
Инициализируем ноду
```bash
teritorid init <name_moniker> --chain-id teritori-1
```
Скачиваем генезис
```bash
wget -O $HOME/.teritorid/config/genesis.json "https://media.githubusercontent.com/media/TERITORI/teritori-chain/v1.1.2/mainnet/teritori-1/genesis.json"
```
Проверяем генезис
```bash
sha256sum ~/.teritorid/config/genesis.json
# daa42b259c5db6a602cb8cf0691a866839494b9ed550c529665fdc857bd68d43
```
Скачиваем addrbook.json
```bash
wget -O $HOME/.teritorid/config/addrbook.json "http://65.108.6.45:8000/teritori/addrbook.json"
```
Создаем сервисный файл (1 команда)
```bash
sudo tee /etc/systemd/system/teritorid.service > /dev/null <<EOF
[Unit]
Description=teritorid
After=network-online.target

[Service]
User=$USER
ExecStart=$(which teritorid) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
```bash
systemctl daemon-reload && \
systemctl enable teritorid && \
systemctl restart teritorid && journalctl -u teritorid -f -o cat
```
# Создание валидатора
Создаем кошелек
```bash
teritorid keys add <name_wallet> --keyring-backend os
!!!Сохраняем seed!!!
```
Создаем валидатора
```bash
teritorid tx staking create-validator \
--chain-id teritori-1 \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--amount 1000000utori \
--pubkey $(teritorid tendermint show-validator) \
--moniker "<name_moniker>" \
--from <name_wallet> \
--fees 555utori
```
