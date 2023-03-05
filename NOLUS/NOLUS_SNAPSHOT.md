<img src="https://github.com/romanr95/GUIDS/blob/main/NOLUS/LOGO_NOLUS.png" width="1050" alt="" />

# SNAPSHOT (1251645 block)
1. Node stop
```
sudo systemctl stop nolusd
```
2. We make a backup of the validator key
```
cp $HOME/.nolus/data/priv_validator_state.json $HOME/.nolus/priv_validator_state.json.backup
```
3. Delete the .data directory and create an empty directory
```
rm -rf $HOME/.nolus/data/
mkdir $HOME/.nolus/data/
```
4. Downloading the archive
```
wget http://65.108.199.120:8000/nolusdata.tar.gz
```
5. Unpacking the archive
```
tar -C $HOME/ -zxvf nolusdata.tar.gz --strip-components 1
```
6. We put the previously saved validator key
```
mv $HOME/.nolus/priv_validator_state.json.backup $HOME/.nolus/data/priv_validator_state.json
```
7. Starting the node
```
sudo systemctl restart nolusd && journalctl -fu nolusd -o cat
```
