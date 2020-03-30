# Hosting a discord.py bot

## Introduction

In this article you will learn:

* What is needed to host a discord.py Bot.
* How to keep the bot running after closing the SSH session.

In this article, we assume that:

* You are using discord.py as your library
* You have confirmed your bot is working locally.
* Know how to establish an SSH connection.
* Know how to use FTP/SFTP to upload files.
* You have a Linux VPS (Debian/Ubuntu).

## Installation

To host a bot using discord.py on a server, you must install Python. If you're using a distro like Ubuntu 18.04 or Debian 10, then the appropriate version should already be installed.

!!! note
    Since Debian 8 and lower doesn't have the required Python version that discord.py requires. You can always
    use a tool like [`pyenv`](https://github.com/pyenv/pyenv) to easily compile and install any Python version 
    that you want, however it won't be covered in this topic.

For Ubuntu, you would do
```sh
$ sudo add-apt-repository ppa:deadsnakes/ppa
$ sudo apt update
$ sudo apt install python3.7 python3.7-venv python3-pip
```

For Debian, you would do
```sh
$ sudo apt update
$ sudo apt install python3 python3-venv python3-pip
```

Congrats! You now have Python 3.7 installed with Pip. Now we can install the required dependencies for the bot 
to run.

```sh
$ cd mybotdir
$ python3.7 -m venv venv 
$ source venv/bin/activate
$ pip install -U discord.py # Append [voice] if you use any voice functionality
```

!!! info    
    `cd mybotdir`
        <br />
    Change the directory to your bot's root directory. Make sure to replace `mybotdir` with the correct path of your project's directory
        <br />  
    `python3.7 -m venv venv`
        <br />
    This creates something known as a virtual environment in Python. This is optional, but it's recommended
    to use virtual environments to prevent conflicts with other packages that you install. See the [official documentation](https://docs.python.org/3/library/venv.html#creating-virtual-environments) for more information.    
        <br />
    `source venv/bin/activate`
        <br />
    Activates the virtual environment you are about to work with. You can alternatively execute a command such as `venv/bin/python` and `venv/bin/pip` if you don't
    feel like activating it.
        <br />

## Running your bot

We installed our required dependencies and there's only one last thing to do, run the bot. Assuming your bot's code is fine you can
just run the bot.

```sh
$ python bot.py
```

!!! warning
    Encountering a `ModuleNotFound: No module named 'discord'` when trying to run the command above? Chances are is
    you didn't activate the virtual environment or install the dependency.

Now you can close your SSH client, but chances are is your bot will stop as well which can be problematic. You can solve this by
using a process manager such as `systemd` or you can use a terminal multiplexer like `screen` to keep the session
alive when you log out of SSH. Read further down to learn how to utilize these programs.


### Systemd

The most common init system you'll ever find on a Linux distro. You can open your favourite text editor
such as `vim` or `nano` to create a file at `/etc/systemd/system/my_bot.service` (replace my_bot with your bots name if you prefer).

```ini
[Unit]
Description=My own little bot!
After=multi-user.target

[Service]
WorkingDirectory=/home/my_username/my_bot_name
# This is where your bot's code lies
User=my_username 
# It's discouraged to run your bot as root
ExecStart=/bin/bash -c "source venv/bin/activate && python bot.py" 
# Activate the virtual env and start the bot within it
Restart=always 
# Restarts the bot regardless of exit code

[Install]
WantedBy=multi-user.target
```

Now you would enter `systemctl daemon-reload` to reload the service files. 

#### Useful commands when managing a bot under systemd 

```sh
systemctl enable my_bot  # Enables your bot to start at server init
systemctl disable my_bot # Your bot won't start at server init anymore
systemctl start my_bot   # Starts your bot
systemctl stop my_bot    # Stops your bot
systemctl restart my_bot # Restarts your bot
```

### Screen

Most beginners would prefer this option as it's pretty straightforward, but it doesn't offer
you the features a process manager would offer such as starting, stopping, and restarting your bot as `systemd` would.

```sh
# Creating a session named botto
$ screen -S botto
# Listing all of your sessions in your user
$ screen -ls
There is a screen on:
        25812.botto     (03/25/2020 02:44:43 AM)        (Attached)
1 Socket in /run/screen/S-someuser
# Exiting out of the session without losing anything
# CTRL-A CTRL-D

# Reattaching to your bot's session
$ screen -r botto
# To stop your bot you can hit CTRL-C
```
