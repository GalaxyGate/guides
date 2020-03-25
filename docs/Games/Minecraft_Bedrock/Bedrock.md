# Hosting a Bedrock Server

## Introduction
In this guide, you will be able to host a Bedrock Dedicated Minecraft server as well as keeping it alive without having to be logged in all the time. Before you start, make sure you meet the requirements and have a basic understanding of Linux, if you have issues or are stuck on a step you can ask for help in our [support server.](https://discord.gg/jcKEyxn)

## Requirements 
* You have a Linux VPS (Debian/Ubuntu).
* You are logged in as root or have a user that can use sudo.
* You know how to establish an SSH connection or use an SSH client.
<br/>

*If you do not know how to connect to your server I would recommend using an SSH client like [bitvise](https://www.bitvise.com/ssh-client-download) or [Putty](https://www.putty.org/) and ask for help in our [support server.](https://discord.gg/jcKEyxn)*

## Creating a user for Minecraft
For security purposes, Minecraft should not be running under the root user. We will create a new system user and group with home directory /opt/Minecraft that will run the Minecraft server:

```
sudo useradd -r -m -U -d /opt/Minecraft -s /bin/bash Minecraft
```
We are not going to set a password for this user. This is a good security practice because this user will not be able to log in via SSH. To login to the Minecraft user, you’ll need to be logged in to the server as root or user with sudo privileges.

Before starting with the installation process, make sure you switch to the Minecraft user.

```
sudo su - Minecraft
```

## Creating the Directory 
If you plan to have multiple versions of Minecraft running I would recommend creating a folder for them to make sure the files do not conflict.
```
mkdir Bedrock
cd Bedrock
```

## Getting the files
Install the zipped files required to host the server using the command provided below, the command will also unzip the files and install unzip which is required.
```
sudo apt install unzip -y && wget https://minecraft.azureedge.net/bin-linux/bedrock-server-1.14.32.1.zip && unzip bedrock-server-1.14.32.1.zip && rm bedrock-server-1.14.32.1.zip
```

## Starting the server
Use the command below to start the server, please make sure you are in the same directory as the files are located in.
```
LD_LIBRARY_PATH=. ./bedrock_server
```
![MC_Pic](https://i.imgur.com/SSbHExv.png)

## Connecting to the server
You should grab the IP of your server which can be found using the command below if you do not know it.

```
dig +short myip.opendns.com @resolver1.opendns.com
```
 Copy the IP and open Minecraft up, go to servers and click add a server and under `Server Address` put the server's IP in and click Save.
 ![MC_SERVER](https://i.imgur.com/mmChKDk.png)


## Keeping your server alive

!!! Warning
    First, we should exit from the Minecraft user so we can install any of the solutions below as the user does not have any root permissions.
Run the command below to switch to root or the user you were logged in as.
```
exit
```
### Screen

Screen is one way of keeping your server running in the background without having to keep your SSH session open.

!!! warning
    `screen` does not boot on load or write logs to the disk automatically, reboots would kill the screen due to only being a virtual session.

To start your server with screen, first, make sure you have `screen` package installed.

#### Installation
You can install screen using the one-liner below:
```bash
sudo apt update && sudo apt install screen -y
```
#### Usage

!!! Information
    You will want to log back into your Minecraft user before executing any of the commands below.
```
sudo su - Minecraft
```
Install the required files.
```
wget https://gist.githubusercontent.com/dragonblitz10/a7a0c8d52834fe6798924b7ce1c72e21/raw/2b6f88d31995a37f62641d591a9d39a218e6f78e/Server.sh -O Server.sh
chmod +x Server.sh
```
You can then start your server by using the command below:
```bash
screen -S Bedrock ./Server.sh
```
This should create a session you can safely leave without fear of it shutting down when you leave. 
You can leave the screen via `CTRL+AD` from this session so your Server is still online when you leave.

You can re-attach to the running screen by running `screen -r Bedrock` and either issue commands or shutdown the server via `CTRL+C`.