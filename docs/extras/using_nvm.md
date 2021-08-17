# Using Node Version Manager
### Introduction
Node version manager allows you to change your NodeJS version with a few simple commands.
![nvm](https://techout.is-a.dev/SG323g3z)
#### That above is the base screen when you run `nvm`
## Here's how to install node version manager (nvm)
First of all, let's start by updating and upgrading everything.
You can update and upgrade everything by running the following:
```
apt-get update && apt-get upgrade 
```
After that, Run the following to install:
```
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```
After that finishes running, run the following:
```
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```
This will load nvm and let you use it.
### Installing NodeJS
In this example, we'll be installing Node v16.
To install, just run the following:
```
nvm install v16
```
Should look like this:

![nvm_install](https://techout.is-a.dev/jp6csc91)

If for some reason it doesn't start using it automatically after installed, run this:
```
nvm use v16
```
If the version isn't available it'll provide an error saying to install it.

### This concludes the tutorial, this can only be used on linux machines.
