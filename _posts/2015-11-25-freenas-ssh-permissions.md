---
layout: post
title: "Setting Up Freenas with SSH Keys"
published: true
---

Recently I have been playing around with how to remotely log into my NAS. For a while I have had it so that I have to pass my password into my terminal to login. However, this limits my ability to script my computer to do things that would send data back and forth without me interfering. Well, that just wouldn't do so I set out to find a way around this.

To preface this, I am not going over how to setup a Freenas server or anything else. This is for post setup.

### The Problem
The problem is that Freenas (9.2 at least since that is what I am using) does not properly set permissions for user accounts when it is creating them, which causes a conflict when setting up SSH Key authentication. Try ans you may, you probably will run into a `private key error` of some sort. 

I had to do a ton of searching on Google to find that one random thread with the fix, but I found it!

### The Solution
Freenas should be as simple as:
1. On your computer, run `ssh-keygen` and create a key.
2. Navigate to your `~/.ssh/` folder and find your `id_rsa.pub` file. Copy the contents
3. Select the user you want in your Freenas user list, and paste the contents of the prior file in the area that says "SSH Public Key". 
4. Hit save.

HOWEVER, hitting save may not work because I found I still had an issue. After I hit save I had to do the following steps:
1. Log into your Freenas WebGUI and open up the shell.
2. `chmod 755 /mnt` and then hit enter
3. `chmod 755/mnt/media` <- Where media is probably your data set (mine is called `Data`). Again hitting enter.
4. `chown yourUsername /mnt/mount/yourUserFolder` <- This tells the system that your user owns the folder.
5. `chmod 700 /mnt/mount/yourUserFolder` <- Secures your home directory
6. Close the shell and head over to your other computer that has your `id_rsa` on it. SSH into your NAS `ssh yourUsername@FreeNasIP`. You will be asked for your password. If you aren't and it fails, got to the SSH settings on your NAS and set it to ask for passwords.
7. Now that you are in, create a `~/.ssh` directory. Do this by running `mkdir .ssh`
8. Finally we want to upload your public key. Type `exit` so you disconnect, and then navigate to your local `~/.ssh` folder. Run the following command (with proper parameters) so that we upload your `id_rsa.pub`: 
```bash
cat id_rsa.pub | ssh yourUsername@FreeNasIP 'cat >> ~/.ssh/authorized_keys'
ssh yourUsername@FreeNasIP 'chmod -R 700 ~/.ssh' 
```
9. Now you should be good to log into your server without being asked for a password. Simply type `ssh yourUsername@FreeNasIP` and you will connect.

This bug seems really agrivating, and honestly it took me way too long to figure out what it was and how to fix it. For reference, this answer is [from this forum post](https://forums.freenas.org/index.php?threads/passwordless-ssh-cannot-get-it-to-work-what-so-ever-please-help.542/). 

Hopefully this helps some of you out there. Up next, how to automate your Plex uploads!
