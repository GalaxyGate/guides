- [Hosting a Discord Bot on Linux VPS](#hosting-a-discord-bot-on-linux-vps)
  * [Python](#python)
    + [Requirements](#requirements)
      - [What will you need](#what-will-you-need)
      - [Installation](#installation)
    + [Starting Bot](#starting-bot)
  * [JavaScript](#javascript)
    + [Requirements](#requirements-1)
      - [What will you need](#what-will-you-need-1)
      - [Installation](#installation-1)
    + [Starting Bot](#starting-bot-1)
  * [How to keep bot "up" after ending SSH session](#how-to-keep-bot--up--after-ending-ssh-session)
    + [Using Screen](#using-screen)
    + [System.d Service](#systemd-service)
      - [Creating service](#creating-service)
      - [Starting and Stopping service](#starting-and-stopping-service)
      - [Useful Commands to services](#useful-commands-to-services)
    + [PM2](#pm2)
      - [Installation](#installation-2)
      - [Usage](#usage)
      - [What about autostart?](#what-about-autostart-)

## Python
       
## JavaScript

### Requirements  
 #### What will you need
   * Node.JS
   * Bot Library
    
 #### Installation 
   * Node.js  
    in this example we will download node 12.x  
    Command used to do this is as follows:  
        ```
        curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
        sudo apt-get install nodejs
        ```  
This command downloads package from official distribution of Node.js for Debian based Operating systems (such as Ubuntu) afterward installs it, simple and fast, this is best way to install node.js
* Bot library  
 Once you install node you need to install library your bot uses, for Eris command looks like follows  
        `
        npm install --no-optional eris
        `
 For Discord.js Command looks like this:
       `
       npm install discordjs/discord.js
       `
 Both commands install libraries **without** Voice Support.
 
 And that's it all baseline prerequisites! Let's head to starting it
 
 ### Starting Bot  
 So, we have all prerequisites for basic bot, let’s start it! 
 It is surprisingly easy in both libraries, its literally same command.
 To start bots written in JavaScript you use command
 `
 node example.js
 `
 Where `example` is name of your bot file.
 
 
 ## How to keep bot "up" after ending SSH session  
 So, by now you have working bot, you happily close SSH client then notice with fear in eyes that it went down, you check logs, but there are no errors.
 
 Don't worry this is **normal**. In Linux SSH when you run something its ran under SSH process, because of that when you close session it kills also all processes under it. I hear you asking "How to keep my bot up then?", well there are few ways, one of them is Screen
 
 ### Using Screen
 Screen is simplest solution, it requires no configuration and Ubuntu has it even preinstalled. It is also easiest to use. to start input `screen` into Terminal, you will see output like this:  
 ![Screen start]( https://cat.girlsare.life/5rau9hY.png "Screen")    
 It means that screen started successfully, now start your bot using instructions above. When you see that it successfully started press `ctrl+a` you now told screen to listen for keybind command, now if you press `ctrl+d` it will "detach" screen into independent process, now you can safely end SSH session and bot will stay up.

If you will need to access display with bot output just start SSH, input `screen` again and then input `screen -r` to view all screen, they will look like this `5051.pts-0.hostname_of_vps    (10/18/2019 02:08:53 AM)        (Detached)` notice 4 digits on beginning of line, it is PID of that screen session, to access it use command `screen -r 5051` where 5051 is PID of screen you want to access  
  
Screen has some shortcomings however: 
     - If you need to see bot output in console you need to always reattach screen
     - If Screen crash bot crash too
     - It does not provide auto start

### System.d Service
Another good solution is running your code as System Service using System.d, Setting that up is bit more complicated than using screen but it solves main shortcomings of Screen, Crashes and Auto start. Logs are saved and viewable in service information, Running code as service also makes it independent from any software apart from: Interpreter and Linux itself.

#### Creating service  
Create file using your favorite editor in path `/lib/systemd/system/exmple.service` content of file should look like this for JavaScript bot:
```
[Unit]
Description=Bot
After=multi-user.target
[Service]
WorkingDirectory=/home/username/botusername
User=username
ExecStart=/usr/bin/node /home/username/botusername/bot.js --no-prompt
Type=idle
Restart=always
RestartSec=15

[Install]
WantedBy=multi-user.target
```  
You need to replace username with your account (Linux, not discord) username (remember to do it both in `User` and `ExecStart`!) and in `ExecStart` replace `bot` in `bot.js` with name of your bot main file and edit description to whatever you want, it’s there just so you know what service is responsible for. Syntax is literally same on Python apart from ExecStart line which looks like this:

`ExecStart=/usr/bin/python3 /home/username/botusername/bot.py --no-prompt`  
  
same rules apply to editing lines as in JS version of Service file.

After you finish creating file, make sure everything is good then press `ctrl+x` **SAVING CHANGES** then run command: `sudo systemctl daemon-reload` which will reload services to accept new one and `sudo systemctl enable example.service` (remember to replace example with your service file name) which will enable service, and done! service is ready to be used

#### Starting and Stopping service
So now that we have service ready we need to start it, it is easy, just use following command:  
`sudo systemctl start example.service`  
(as always, replace example with service file name) and bot should start, you can check if service is running properly using:  
`sudo systemctl status example.service`  
command to print information about it.

#### Useful Commands to services
here is list of most useful commands for services in one place

```
sudo systemctl stop example.service          #To stop running service 
sudo systemctl start example.service         #To start running service 
sudo systemctl restart example.service       #To restart running service 
```

### PM2

Another way, although requiring install of another software is PM2 which is a process manager that make sure that processes stay alive after closing SSH. While it is advertised mainly for JavaScript applications, it supports other languages, explicitly showing Bash, Python and Binary files in it documentation.



#### Installation

Installation of PM2 is simple one-command process, first you need to make sure you are running Node.JS on your VPS due to it being requirement. 

After making sure Node is installed use: `npm install pm2@latest -g` to install PM2, and done!



#### Usage

PM2 usage is as simple as it installation. To start code you use `pm2 start example.js` where `example.js` is file you want to run, recommendation from my side would be adding switch `-name example` which can be used in control commands. For Example: `pm2 start example.js -name example_program`



To control app running under PM2 you can use following commands:

```

pm2 restart app_name

pm2 reload app_name

pm2 stop app_name

``` 

App name can be either PID of process or name assigned with `-name` switch in command, to check all apps running under PM2 you can use `pm2 ls` which will print result similar to this:

![PM2 list](https://i.imgur.com/LmRD3FN.png "pm2 ls") 

and to display real-time logs you can use `pm2 logs` (for historical entries: `pm2 logs --lines 200` where `200` is amount of lines you want to go back)



#### What about autostart?

PM2 by default don’t provide auto start, however it has built in script that enables it as feature. PM2 will automatically start all workers under it on restart if you will use this feature.



To enable auto start use `pm2 startup` command
