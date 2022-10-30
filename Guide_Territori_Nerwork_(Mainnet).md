<img src="https://github.com/romanr95/MONITORING/blob/main/uptimekuma_1%20(2).png" width="400" alt="" /># 
Teritori Network (Mainnet) Установка ноды и создание валидатора
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
