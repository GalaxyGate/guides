# Using tmate (Collaboration tool)
### Introduction
Tmate allows you to invite another user to your server without giving any ssh passes or adding ssh keys.
![tmate](https://techout.is-a.dev/BD4qMvGX)
### How to install tmate
Run the following to install the latest version of tmate available: 
```
apt-get install tmate
```
That should install the latest tmate version available on the mirror you're using.
If it errors, try doing the following:
```
apt-get install git-core build-essential pkg-config libtool libevent-dev libncurses-dev zlib1g-dev automake libssh-dev libmsgpack-dev
git clone https://github.com/tmate-io/tmate.git
cd tmate
./autogen.sh
./configure
make
make install
```
Now that tmate is installed, let's get to how to run it.
### How to run tmate
This step is quite simple, all you have to do is run the following:
```
tmate
```
All you have to do after you run that is copy which session you want to share and send it to the user you're wanting to share it with.
