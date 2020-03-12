# Creating a Sudo user account

## Introduction
The sudo command allows you to run programs with the security privileges of another user (by default, as the superuser). It prompts you for your personal password and confirms your request to execute a command by checking if you are allowed to use sudo or not.


## Creating a new user 
Using the adduser command you should create a user account for the Linux vps you are on.
```bash
adduser username
```
!!! warning
    Be sure to replace username with the user that you want to create.

You then want to set a password which you will need to type twice, keystrokes aren't logged for security purposes so do not be alarmed if you do not see anything happening on the screen.
```bash
Set password prompts:
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```
After setting a password you will be asked to fill in some basic user information, this is completely optional so you can just click enter a couple of times!
```bash
User information prompts:
Changing the user information for username
Enter the new value, or press ENTER for the default
    Full Name []:
    Room Number []:
    Work Phone []:
    Home Phone []:
    Other []:
Is the information correct? [Y/n]
```
!!! warning
    Make sure to type in `Y` and click enter otherwise you will have to redo the user infomation prompht!
## Granting user sudo
Now we have added a user that you can login into via ssh, it is now time for us to allow the user to use sudo. We will be using the `usermod` command to add `username` to the `sudo` group.
```bash
usermod -aG sudo username
```
Now we will test that if the user we have added is allowed to use sudo or not.
Use the su command to login to the username you have selected.
```bash
su - username
```
As you want to check if you have sudo you should run the command below to effectively test if you have sudo or not.
```bash
sudo ls /root
```
Since only root can read that directory you would require to be either root or have sudo to access the directory.

Response if you have sudo:
![sudo responde](https://i.imgur.com/UumT15w.png)

Response if you don't have sudo:
![permission denied sudo](https://i.imgur.com/90u5qng.png)