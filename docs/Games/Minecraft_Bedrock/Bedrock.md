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