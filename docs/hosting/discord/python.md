# Hosting a discord.py bot

## Introduction

In this guide you will learn:

* What is needed to host a discord.py Bot.
* How to keep the bot running after closing the SSH session.

In this guide, we assume that:

* You're logged in as root or using a sudo user.
* You are using discord.py as your library.
* You already have your bot's files uploaded to the server.
* You have confirmed your bot is working locally.
* Know how to establish an SSH connection.
* You have a Linux VPS (Debian/Ubuntu).

*Don't know how to connect to your server? Check out this [guide.](../../basics/first_login.md)*

## Installation

In this section, we aim to install python3.8 to the server, note that debian users should be running these commands as `root` while ubuntu users can either use `root` or run these commands with `sudo` as a `sudo user`.

!!! note
    Since Debian 8 and lower don't have the required Python version that discord.py requires. You can always
    use a tool like [`pyenv`](https://github.com/pyenv/pyenv) to easily compile and install any Python version 
    that you want, however it won't be covered in this topic.

For Ubuntu, you would do:
```sh
sudo apt update
sudo apt-get install software-properties-common -y
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt install python3.8 python3.8-dev python3.8-venv python3-pip -y
```

For Debian, you would do:
```sh
echo "deb http://http.us.debian.org/debian/ testing non-free contrib main" >> /etc/apt/sources.list
apt update
apt install python3 -y
```

Congrats! You now have Python 3.8 installed with pip. Now we can install the required dependencies for the bot 
to run.


Change to the path of your bot.

```
cd discord_bot
```
!!! note
    You should replace **discord_bot** with the folder you uploaded to the server

**Recommended: Create a virtual enviroment and activate it.**
```
python3.8 -m venv venv
source venv/bin/activate
```
!!! info
    A virtual environment, or most commonly referred to as a venv, is an isolated environment for projects so
    you don't have to worry about polluting the global namespace. See the [official documentation](https://docs.python.org/3/library/venv.html#creating-virtual-environments) for more information.

    You do not need to create a new one every time you start, modify or stop the bot.

Install the `discord.py` package.

```
pip install -U discord.py
```

!!! note
    If you use discord.py's voice features, then i would suggest appending `[voice]` at the end of the command.

!!! note
    To install from a `requirements.txt` file, simply run `pip install -Ur requirements.txt` instead.


## Running your bot

We installed our required dependencies and there's only one last thing to do, run the bot. 

To run your bot you would simply do

```sh
python bot.py
```

!!! note
    If you use a file like `launcher.py` to start your bot, replace `bot.py` with that instead.

!!! warning
    Encountering a `ModuleNotFound: No module named 'discord'` when trying to run the command above? Chances are
    you didn't activate the virtual environment or install the dependency.

Now you can close your SSH client, but chances are is that your bot will stop which can be frustrating. Luckily, you can solve this by
using a process manager such as `systemd` or a terminal multiplexer like `screen` to keep the session alive when you close your SSH connection. Read further down to learn how to utilize these programs.


### Systemd

The most common init system you'll ever find on a Linux distro.

To create and start editing a service file called `my_bot`, you would do

```
nano /etc/systemd/system/my_bot.service
```
!!! note
    Replace `nano` with the editor you choose, otherwise leave it be.

!!! note
    Replace **my_bot** with the name of your bot

We will just use this boilerplate for our bot, make sure you change this to
the appropriate values.

```ini
[Unit]
Description=My Python Discord Bot
After=multi-user.target

[Service]
WorkingDirectory=/root/discord_bot
# This is where your bot's code lies
User=root
# Change user if you aren't using root

# Activates your virtual environment and starts the bot
# replace bot.py with whatever Python file that starts your bot
ExecStart=/bin/bash -c "source venv/bin/activate && python bot.py" 

# This will restart your bot if your bot doesn't return a 0 exit code
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

!!! note
    If you don't use virtual environments, then you can just reassign the `ExecStart` part to `ExecStart=/usr/bin/python3.8 bot.py`

To save these changes in `nano` you would simply type CTRL-X to save your change, `y` to confirm the change, and `Enter` to write that change
to `/etc/systemd/system/my_bot.service`


Now you would enter this command to tell systemd to see your newly created or changed
service files.

```sh
systemctl daemon-reload
```

To start your bot, you would execute

```
systemctl start my_bot
```

To stop your bot, you would execute

```
systemctl stop my_bot
```

To restart your bot, you would execute

```
systemctl restart my_bot
```

To start your bot on VPS start, you would simply do

```
systemctl enable my_bot
```

and to revoke that you would do

```
systemctl disable my_bot
```

### Screen

Most beginners would prefer this option as it's pretty straightforward, but it doesn't offer
you the features a process manager would offer such as starting, stopping, and restarting your bot as `systemd` would.


To create a session called `my_bot`, you would do
```
screen -S my_bot
```

!!! note
    Feel free to replace **my_bot** with something else.

You may have noticed that it cleared your screen, which is completely normal. This session will stay here forever until you explicitly close it or if the server shuts down.


In order to get your bot running, you must activate the virtual environment

```
source venv/bin/activate
```

Now, since you have your dependencies already installed and your virtual environment activated, you can start the bot with the following.

```
python bot.py
```

!!! note
    Replace `bot.py` with whatever file you use to start your bot


Now you can simply disconnect from the session with `CTRL A` and `D` or just close the connection with your server. Congrats, your bot is running on a VPS without the need to keep your computer on.

Now if you would like to re-attach to `my_bot`'s session you would simply
enter

```
screen -r my_bot
```
!!! note
    If you used a different name don't forget to use that. `screen -ls` will also list all active screens.

To detach from the bot's session without closing it you would simply hit `CTRL A` and `D` with your keyboard.

To see how many screen sessions you have, simply enter

```
screen -ls
```

You should see a output similar to this

```
equity@server:~$ screen -ls
There is a screen on:
        4146.my_bot     (03/30/2020 12:09:15 AM)        (Detached)
1 Socket in /run/screen/S-equity.
equity@server:~$
```

!!! note
    The `(Detached)` on the far right means we aren't in the virtual session, otherwise it would
    be `(Attached)`

### Nohup
On many Linux systems (if not all), including Ubuntu and Debian, you can use nohup to run your bot even after you have closed a session.
To run nohup on your discord bot, you can do this, where bot.py is the name of your bot startup file.
```
nohup python3.8 bot.py &
```
This will create a file in your current directory called 'nohup.out'. You can redirect it to a file such as 'logs.txt' by running this instead:
```
nohup python3.8 bot.py &> logs.txt &
```
Tada! Your bot is online, and you can close the session without the bot dying.

If you want to turn off the bot, things get a bit more complicated. You need to either get the PID of the bot's process, then kill it, or make a command in the bot for owners that calls ``os._exit(1)``. In this section, we'll cover getting the PID of the bot's process.
To get the PID of the bot, you can run this:
```
$ ps -ef
```
This will display a large list of running processes. You'll need to find the line that has "python3 bot.py" in the rightmost column, then find the PID of that line (it's in the second column). Once you have that PID, you can run ``kill <pid>``, and the bot will gracefully free used memory, and shut off. Now, you can run the bot again :)


## Resources
* [discord.py Docs](https://discordpy.readthedocs.io/en/latest/)
* [discord.py GitHub](https://github.com/Rapptz/discord.py)
* [discord.py Discord](https://discord.gg/r3sSKJJ)