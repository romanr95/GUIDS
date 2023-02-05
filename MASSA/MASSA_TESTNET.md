<img src="https://github.com/romanr95/GUIDS/blob/main/LOGO%20MASSA.png" width="1050" alt="" />

# LINKS
```WEBSITE``` - https://massa.net/ <br>
```TWITTER``` - https://twitter.com/MassaLabs/ <br> 
```MEDIUM``` - https://massalabs.medium.com/ <br>
```YOUTUBE``` - https://www.youtube.com/channel/UChVfdvYpn0eFk4B-T7TGmOg <br>
```GITHUB``` -  https://github.com/massalabs/massa/
# INSTALLING AND RUNNING A NODE (TEST.19.1)

# STEP 1
Remove the previous version of the node (if it was installed before).
```
sudo systemctl stop massad
rm -rf $HOME/massa
rm -rf $HOME/massa_backup
rm -rf /etc/systemd/system/massad.service
rm -rf /etc/systemd/system/multi-user.target.wants/massad.service
```
# STEP 2
Download binaries from the official GitHub.
```
cd /root
wget https://github.com/massalabs/massa/releases/download/TEST.19.1/massa_TEST.19.1_release_linux.tar.gz
```
Unpack the downloaded archive.
```
tar zxvf massa_TEST.19.1_release_linux.tar.gz
```
# STEP 3
Let's write the ip-address of your server in the configuration file.
```
sudo tee <<EOF >/dev/null $HOME/massa/massa-node/config/config.toml
[network]
routable_ip = "`wget -qO- eth0.me`"
EOF
```
# STEP 4
Run the node and come up with a password.
```
cd $HOME/massa/massa-node/
```
```
./massa-node
```
Node stop. (ctrl+c)
# STEP 5
We create a service file so that the node works in the background.
(Replace "YOUR_PASSWORD" with your password)
```
printf "[Unit]
Description=Massa Node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/massa/massa-node
ExecStart=$HOME/massa/massa-node/massa-node -p YOUR_PASSWORD
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target" > /etc/systemd/system/massad.service
```
We start the node from the service file.
```
sudo systemctl daemon-reload
```
```
sudo systemctl enable massad
```
```
sudo systemctl restart massad
```
View logs.
```
sudo journalctl -f -n 100 -u massad
```
Exit from the logs. (ctrl+c)
# STEP 6
Client launch.
```
cd $HOME/massa/massa-client/
```
```
./massa-client
```
Generation of a new wallet with all keys.
```
wallet_generate_secret_key
```
Password entry.
Registering a wallet address for staking.
```
wallet_info
```
We look at the address of the wallet and add it to the command below.
(Replace "YOUR_WALLET_ADDRESS" with your wallet address)
```
node_start_staking YOUR_WALLET_ADDRESS
```
Checking if the address is staked.
```
node_get_staking_addresses
```
Exit the client (ctrl+c) and check the operation of the node.
```
cd /$HOME/massa/massa-client/ && ./massa-client wallet_info
```
# STEP 7
Go to Discord, to the #testnet-faucet branch.

Request tokens to the wallet address.

Wallet balance check.
```
cd /$HOME/massa/massa-client/ && ./massa-client -p YOUR_PASSWORD wallet_info
```
Buying rolls.
```
cd /$HOME/massa/massa-client/ && ./massa-client -p YOUR_PASSWORD
```
```
buy_rolls YOUR_WALLET_ADDRESS 1 0
```
Checking the purchase of a roll. (Entering a command in the client)
```
wallet_info
```
After 1 hour 40 minutes, the roll will become active and staking of tokens will begin.
# STEP 8.
Registering a node in Discord.
View the IP address of your server.
```
wget -qO- eth0.me
```
Copy the IP address and send it to MassaBot.
Copy USER_ID from MassaBot.
Client launch.
```
cd /$HOME/massa/massa-client/ && ./massa-client -p YOUR_PASSWORD
```
Enter the next command.
```
node_testnet_rewards_program_ownership_proof YOUR_WALLET_ADDRESS YOUR_USER_ID
```
We copy the code that the client issued.
Enter the next command. We check the data from the client with the data from MassaBot.
```
cd /$HOME/massa/massa-client/ && ./massa-client -p YOUR_PASSWORD get_status
```
