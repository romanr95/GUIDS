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
