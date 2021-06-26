# Programming

## Setup Git in WSL

# Source:
Originaly Posted by [PETE O'SHEA](https://peteoshea.co.uk/setup-git-in-wsl/) in August 2018

### Install Git
Git appears to come as standard as part of the WSL install. You can test this by running:
```
$ git --version
git version 2.17.1
```
If for some reason Git is not installed then you can simply pull it down:
```
$ sudo apt install git
```
### Setup global configuration settings
First up you need to configure your name and email address, e.g:
```
$ git config --global user.name "Your Username"
$ git config --global user.email "your@email.com"
```
This is used to mark you as the author of the changes you make. I also recommend turning off the auto line endings feature as this can hide certain issues especially when working with both Linux and Windows. There is another config setting to achieve this:
```
$ git config --global core.autocrlf false
```
If you prefer you can just edit the global config for the current user directly:
```
$ git config --global --edit
```
This does use the default editor, which seems to default to Nano. If you’ve never used Nano before you may prefer to just edit the file directly with something like Vi:
```
$ vi ~/.gitconfig
```
Anyway this file should look something like:

 
### This is Git's per-user configuration file.

```SQL
[user]
        name = Your Username
        email = "your@email.com"

[core]
        autocrlf = false
```
### Checking configuration settings
If you want to check your settings you can run:
```
$ git config --list --show-origin

file:/home/you/.gitconfig      user.name=Your Username
file:/home/you/.gitconfig      user.email=your@email.com
file:/home/you/.gitconfig      core.autocrlf=false
```
This shows you the settings and the value they have been set to. It also shows which configuration file the values come from so if you run this within a git repository for example there may be some extra project specific settings included.

Sharing an existing SSH key between Windows and WSL
If you have an SSH key already setup on Windows you could reuse it rather than creating a new one. (Note that PuTTY keys do not work here). To do this you can just copy the key from the Windows filesystem into the WSL’s filesystem. Linux has some rules about how visible the key is. So you need to make sure the access levels are strict enough to meet these requirements:

```
$ cd ~
$ mkdir .ssh
$ chmod 700 .ssh
$ cd .ssh
$ cp /mnt/c/Users/user/.ssh/id_rsa* .
$ chmod 600 id_rsa
$ chmod 644 id_rsa.pub
```
Creating a new SSH key
If you do not already have an SSH key then you could generate one in WSL:
```
$ ssh-keygen -t rsa -b 4096 -C "your@email.com"
```
Save the key as id_rsa in the .ssh directory in your home directory, e.g. /home/you/.ssh/id_rsa for user you.

As mentioned earlier you will likely want to copy this file back to the Windows system. A good reason for this is that the WSL distro could be uninstalled or a second system could be setup and you don’t want to lose your key or have to setup a new key for each environment on the same machine.
```
$ cd /mnt/c/Users/user
$ ls -la
```
If .ssh isn’t listed then create it now:
```
$ mkdir .ssh
```
Now you can copy these files so that Windows and WSL can use the same key:
```
$ cd .ssh
$ cp ~/.ssh/id_rsa* .
```
This gives you the additional benefit that you have a copy of the key on the main Windows filesystem. This is very useful if you remove the specific WSL Linux distribution app, or want to use multiple distros.

One additional step here though might be to set the id_rsa file to be read-only in Windows. Using chmod from WSL doesn’t seem to work so you’ll have to do this in Windows Explorer.

Your public key
To make use of your key the public key needs to be added to remote systems. This will then allow secure connections using your private key. The details of the public key can be retrieved with:
```
$ cat ~/.ssh/id_rsa.pub
```
Add this public SSH key to the services you use e.g. GitHub and Bitbucket. You may also want to add it to the authorized_keys file on remote servers to allow SSH access using the key.

