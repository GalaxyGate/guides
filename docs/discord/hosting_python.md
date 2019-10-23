# Hosting a Discord Python Bot

## Introduction

In this article you will learn:

* What is needed to host a Python Bot.
* How to make bot run after closing SSH session.

In this article we assume that:

* You have confirmed your bot is working locally.
* Know how to establish an SSH connection.
* Know how to use FTP/SFTP to upload files.
* You have a Linux VPS (Debian/Ubuntu).

## Installation

### Python

For a Python discord bot, you will need to have Python installed on the server.

After logging in to your server using SSH, check if you already have the required version of python installed.
Running `python3.7` should display a screen similar to the one below, indicating that you have Python 3.7 installed.

![python3.7-screen](https://cat.girlsare.life/6LQLQ81.png "Python 3.7 Interpreter")

You can exit by typing `exit()`.  

If the command above failed, you probably don't have Python installed on the server.

You can run the commands below to install Python 3.7.
```bash
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install python3.7
```

!!! question "What do these commands do?"
    `add-apt-repository ppa:deadsnakes/ppa`
    <br/>
    Adds the 'deadsnakes'' repository which hosts Python builds to your system's package manager.
    <br/>
    <br/>
    `apt update`
    <br/>
    Updates the list of software available on the repositories allowing you to install them.
    <br/>
    <br/>
    `apt install python3.7`
    <br/>
    Installs Python in version 3.7, if you need a different version simply replace `3.7` with version you need.    
    
You probably noticed that every command is prefixed with `sudo`. This means that they will be executed with Root privileges. This is necessary because only the system administrator can install packages.

### Discord Library  
Depending on the library your bot uses, you will need a different command.

#### Discord.py

Discord.py has two variants, one with voice support and one without, the commands to install them differ minimally.

**Library with Voice Support**

To install discord.py with voice support use this command:
  
```bash
python3 -m pip install -U discord.py[voice]
```

**Library without Voice Support**

As mentioned earlier, the command is really similar, in reality it is basically same apart from the lack of `[voice]`.

So, if you want to install discord.py without voice support you should use:
```bash
python3 -m pip install -U discord.py
```

#### Disco

If your Discord bot uses Disco as the library, you can run the following command to install it.

```bash
python3 -m pip install disco-py
```

## Running the Bot
You can now upload your bot files to the server. If you uploaded it to a folder, please navigate to it using `cd folderName`.

#### Discord.py

You can now start your bot using:
```bash
python3 example.py
```
where `example.py` is your bot main file.

#### Disco

If your bot uses Disco as the library:

```bash
python3 -m disco.cli --token="MY_DISCORD_TOKEN" --run-bot --plugin simpleplugin
```

If everything went ok so far, you should see your bot come online on Discord. Your bot is now running from the terminal session
you are using, so if you close your SSH client, your bot will be shutdown.

!!! tip
    This is an important test to do before installing it as a system service below, as it will properly log any errors or missing
    files/dependencies that you might need. 

You can now kill the bot using `CTRL+C`.  

## Installing the Bot

If you have successfully ran the bot from the command line above, you are ready to install the bot as a service.

This has some advantages over simply running it:

- Keeps the bot running in the background.
- Auto starts in case of a server restart.

There are several ways of running your bot as a service, you can choose the one which you like the most.

### screen

The easiest way is using screen, although it's not technically running as a service, it works for most tasks.

!!! warning
    `screen` does not have autostart functionality, as it's purely a 'virtual ssh session' just like the one you used to
    first run your bot.

To start a your bot with screen, first make sure you have `screen` installed.

You can install screen using the commands below:
```bash
sudo apt update
sudo apt install screen -y
```

You can then start your bot by using the command below:
```bash
screen -S bot commandHere
```

Replace `commandHere` with the appropriate start command for your bot, for example:
```bash
screen -S bot python3 example.py
```

This will create a new screen 'session' and run your bot on the foreground.

You can now 'detach' (`CTRL+AD`) from this session so your bot keeps running in the background.

You can re-attach to the running screen by running `screen -r bot` and then terminate your bot by using `CTRL+C`.

### systemd

You can also use systemd as the service manager for your bot. This is the recommended approach for Linux.


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

Assuming the bot files are on `/root/example` the file will look like this for a discord.py bot:

```properties
[Unit]
Description=Bot
After=multi-user.target
[Service]
WorkingDirectory=/root/example
User=root
ExecStart=/usr/bin/python3 /root/example/example.py --no-prompt
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