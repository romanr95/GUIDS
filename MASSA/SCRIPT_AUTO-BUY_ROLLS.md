<img src="https://github.com/romanr95/GUIDS/blob/main/LOGO%20MASSA.png" width="1050" alt="" />

# HOW DOES THIS SCRIPT WORK?
The script looks at the "CANDIDATE" parameter once every three minutes, and if it becomes zero, it buys 1 roll, without waiting until the rolls that have flown back into coins.
If the "CANDIDATE" parameter is greater than zero, the script terminates and will be launched in the next 3 minutes. <br>
Why exactly 3 minutes?
To give the client time to process the command. Otherwise, he will buy the next roll in a minute if you have enough coins.
The script is located in the "rollsup.sh" file in the "root" folder. <br>
The script itself, in the process of work, will create a file for logging events. <br>
Runs using the Crontab daemon, which is part of Linux. <br>
All commands presented in this guide are executed from the command line of your terminal. <br>
For the script to work properly, you must have 1 active roll and 100 or more coins on your balance.
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

# DELETE A SCRIPT.
Delete the script.
```
/usr/bin/rm /root/rollsup.sh
```
Delete the script launch file by time.
```
/usr/bin/rm /etc/cron.d/massarolls
```
Delete the log file.
```
/usr/bin/rm /root/rolls.log
```
