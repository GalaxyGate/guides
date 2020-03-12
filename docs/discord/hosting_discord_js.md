# Hosting a Discord.js Bot

## Introduction

In this guide you will learn:

* How to host a Discord.js bot on a vps.
* How to make bot stay online 24/7.

In this guide, I am assuming that:

* You have locally tested the bot and it works.
* You know how to establish an SSH connection or use an SSH client.
* You know how to use FTP/SFTP to upload files or use a deployment method like GitHub.
* You have a Linux VPS (Debian/Ubuntu).
* You are logged in as root or have a user that can use sudo.

## Installation

### node.js

For a JavaScript discord bot, you will need to have node.js and npm installed on the server.
To install node.js & npm run the commands listed.
You can choose which version to install if you have a preference however I recomend that you should choose V12.

V10
```bash
sudo apt update
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash –
sudo apt -y install nodejs
```
V12
```
sudo apt update
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt -y install nodejs
```
V13
```bash
sudo apt update
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_13.x | sudo -E bash –
sudo apt -y install nodejs
```
Verify that node is installed correctly.
```bash
node -v
npm -v
```
![Node Version](https://i.imgur.com/T8u5lvN.png)

If you do not get a response from above or something went wrong, please take a screenshot of what went wrong and ask us in the support channel in our [support server.](https://discord.gg/jcKEyxn) 

### Getting your bot onto the VPS
Assuming you have an SSH client it should be easy enough to drag a zipped copy of your bot without the node_modules folder and unzip it via terminal.
```
cd <bot folder name> // if you haven't already!
sudo apt install unzip -y
unzip filename.zip
npm install 
```
![unzipping](https://i.imgur.com/qZQP3nL.gif)

### Discord.JS

You can now start your bot using either:
```bash
node .  // if you set the main correctly in package.json
```
OR 
```bash
node filename.js // file name being the bot file
```
![node](https://i.imgur.com/Dc18xNa.gif)

If everything went okay at this point of the guide you should notice that the bot should be online in your server, however, you should note that it is running on an SSH session which kills itself when you end the SSH session, to prevent this we have listed a few ways to stop this from happening.

To exit hit Control + C (Ctrl + c)


!!! warning
    I: You should always test things before using the methods to ensure the bot can load up fine.

    II: Avoid committing your token to github if you use that.

    III: Do not copy you node_modules folder use npm install, it saves you time and allows the deps to be built correctly.

## 24/7

If you have successfully run the bot from the command line from above without any errors or issues then you should be ready to set up 24/7 bot hosting to keep the bot online.

This has some advantages over simply running it in an SSH session:

- The bot runs in the background 24/7 unless it crashes.
- The Bot loads itself on reboot so no need to manually run it again.
- Easier to manage via CLI.
- You do not need to actively manage the bot.

There are multiple ways to run the bot 24/7, feel free to choose the best method for you, personally I recommend pm2.
### Screen

One way of achieving a 24/7 bot would be using screen to keep the bot running in the background
!!! warning
    `screen` does not boot on load or write logs to the disk automatically, reboots would kill the screen due to only being a virtual session.

To start your bot with screen, first, make sure you have `screen` package installed.
#### Installation
You can install screen using the one-liner below:
```bash
sudo apt update && sudo apt install screen -y
```
#### Usage

You can then start your bot by using the command below:
```bash
screen -S <BotName> node x
```

*hint:* Replace x with the correct filename for your bot, for example:
```bash
screen -S aero index.js
```
This should create a session you can safely leave without fear of it shutting down when you leave, 
You can leave the screen via `CTRL+AD` from this session so your bot remains working after you leave.

You can re-attach to the running screen by running `screen -r BotName` and then terminate the bot by using `CTRL+C`.

### systemd

You can also use systemd as the service manager for your bot. 


#### Installation

Systemd requires a service description file to be created.

You can create it using your favorite editor and save it to `/lib/systemd/system/bot.service`:
```properties
[Unit]
Description=Bot
After=multi-user.target
[Service]
WorkingDirectory=/root/botFolder
User=root
ExecStart=command --no-prompt
Type=idle
Restart=always
RestartSec=15

[Install]
WantedBy=multi-user.target
```  

You need to replace the proper path for the bot folder as well as the startup command.

Assuming the bot files are on `/root/bot` the file will look like this for a discord.js bot:

```properties
[Unit]
Description=Bot
After=multi-user.target
[Service]
WorkingDirectory=/root/bot
User=root
ExecStart=/usr/bin/node /root/bot/bot.js 
Type=idle
Restart=always
RestartSec=15

[Install]
WantedBy=multi-user.target
```

After you finish creating the file, you need to run `sudo systemctl daemon-reload` which will tell systemd to re-scan for services.

#### Usage

Below are some command usages for systemd.

```bash
sudo systemctl daemon-reload # Re-scans for service changes.
sudo systemctl enable bot # Enables auto-start for the service named 'bot'.
sudo systemctl start bot # Starts the 'bot' service.
sudo systemctl stop bot # Stops the 'bot' service.
sudo systemctl restart bot # Restarts the 'bot' service.
```

### PM2
What is PM2?
PM2 is a process manager for the JavaScript runtime Node.js which can be used to keep node application alive after a killed SSH session. PM2 can easily start/stop/restart your bot as well as provide logging. 

!!! warning
    PM2 will restart when the bot crashes which can result in a boot loop.

#### Installing PM2
Run the following commands...
```bash
npm install pm2@latest -g
```

#### Starting the bot
*note:* Be in the same directory or use absolute paths.

#### Commands
```bash
pm2 start <botfile.js>
```
To check the status
```bash
pm2 ls
```
To check logs 
```bash
pm2 logs 0
```
#### PM2 tips
- Use `pm2 save` to save the processes.
- Use `pm2 resurrect` to revie them in the event of a reboot and the processes did not come back.
- PM2 has an webapp which can be found [here.](https://pm2.keymetrics.io/)
- `pm2 ls` will show all the running node instances.
- PM2 commands can be found [here](https://devhints.io/pm2)