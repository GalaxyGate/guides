# Using tmate (Collaboration tool)
### Introduction
tmate is a clone of tmux (terminal multiplexer) that provides a secure, instant and easy-to-use terminal sharing solution over an SSH connection allowing you to share terminal access without giving away SSH credentials or adding an SSH key.

### Installing tmate
Let's start by updating and upgrading everything
Run the following to update and upgrade everything:
```
apt-get update && apt-get upgrade
```   
After that, run the following to install the latest version of tmate available: 
```
apt-get install -y tmate
```
That should install the latest tmate version available on the mirror you're using.
If it errors, try doing the following:
```
apt-get install -y git git-core build-essential pkg-config libtool libevent-dev libncurses-dev zlib1g-dev automake libssh-dev libmsgpack-dev
git clone https://github.com/tmate-io/tmate.git
cd tmate
./autogen.sh
./configure
make
make install
```
Now that tmate is installed, let's get to how to run it.
### Using tmate
This step is quite simple, all you have to do is run the following:
```
tmate
```
All you have to do after you run that is copy which session you want to share and send it to the user you're wanting to share it with.

![tmate](../assets/tmate/help.png)

### Resources
* [Website](https://tmate.io/)
* [Repo](https://github.com/tmate-io/tmate/)