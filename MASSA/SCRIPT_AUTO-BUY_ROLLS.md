<img src="https://github.com/romanr95/GUIDS/blob/main/LOGO%20MASSA.png" width="1050" alt="" />

# STEP 1
Go to the "root" folder.
```
cd /root
```
<img src="https://github.com/romanr95/GUIDS/blob/main/MASSA/24.png" width="1050" alt="" />

# STEP 2
Create a file "rollsup.sh". Copy the entire command and paste it into the terminal.
```
sudo tee /root/rollsup.sh > /dev/null <<EOF
#!/bin/sh
#Версия 0.14
cd /root/massa/massa-client
#Set variables
catt=/usr/bin/cat
passwd=\$(\$catt \$HOME/massapasswd)
candidat=\$(./massa-client wallet_info -p "\$passwd"|grep 'Rolls'|awk '{print \$4}'| sed 's/=/ /'|awk '{print \$2}')
massa_wallet_address=\$(./massa-client -p "\$passwd" wallet_info |grep 'Address'|awk '{print \$2}')
tmp_final_balans=\$(./massa-client -p "\$passwd" wallet_info |grep 'Balance'|awk '{print \$3}'| sed 's/=/ /'|sed 's/,/ /'|awk '{print \$2}')
final_balans=\${tmp_final_balans%%.*}
averagetmp=\$(\$catt /proc/loadavg | awk '{print \$1}')
node=\$(./massa-client -p "\$passwd" get_status |grep 'Error'|awk '{print \$1}')
if [ -z "\$node" ]&&[ -z "\$candidat" ];then
echo \`/bin/date +"%b %d %H:%M"\` "(rollsup) Node is currently offline" >> /root/rolls.log
elif [ \$candidat -gt "0" ];then
echo "Ok" > /dev/null
elif [ \$final_balans -gt "99" ]; then
echo \`/bin/date +"%b %d %H:%M"\` "(rollsup) The roll flew off, we check the number of coins and try to buy" >> /root/rolls.log
resp=\$(./massa-client -p "\$passwd" buy_rolls \$massa_wallet_address 1 0)
else
echo \`/bin/date +"%b %d %H:%M"\` "(rollsup) Not enough coins to buy a roll from you \$final_balans, minimum 100" >> /root/rolls.log
fi
EOF
```
<img src="https://github.com/romanr95/GUIDS/blob/main/MASSA/25.png" width="1050" alt="" />

# STEP 3
We add a task to the cron daemon to execute the file "rollsup.sh" every three minutes.
The task will be in the "massarolls" file and located in the "cron.d" folder.
```
printf "SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
*/3 * * * * root /bin/bash /root/rollsup.sh > /dev/null 2>&1
" > /etc/cron.d/massarolls
```
<img src="https://github.com/romanr95/GUIDS/blob/main/MASSA/26.png" width="1050" alt="" />

# STEP 4
We register the password for access to the client in a separate file.
(Replace "YOUR_PASSWORD" with your password)
```
sudo tee $HOME/massapasswd > /dev/null <<EOF
YOUR_PASSWORD
EOF
```
<img src="https://github.com/romanr95/GUIDS/blob/main/MASSA/27.png" width="1050" alt="" />
