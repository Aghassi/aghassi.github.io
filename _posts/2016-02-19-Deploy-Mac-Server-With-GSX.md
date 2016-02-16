---
layout: post
title: "Deploying a Mac Server with GSX"
published: true
---

Recently, I was asked by a friend to fix a Mac server I had set up a while back. Unfortunately, the server was borked beyond repair, so a reinstalllation of the OS was necesssary. This did cause me to look back at the guide I wrote for myself back in 2013. Below is an editted version of that guide for posterity should anyone else need it. I know it helped my friend setup a new server just fine. The guide is a tad dated, but not much has changed in the past three years (luckily).

----

## INTRO

* This is a compilation of the knowledge I gained doing this
* Everything here is possibly deprecated by the time you read this, keep yourself updated
* Be willing to fail and you will make it


### General Rules

* DON'T TOUCH THE SERVERS UNLESS YOU KNOW WHAT YOU ARE DOING
* I recommend not having more than 8 computers per server until we figure out how to get more servers in an array of sorts. For load balancing reasons, that is all.
* If you do use the computers for something, please delete anything you put on them. It doesn't need to be cluttered and slow because it is a server.


### Current Setup

There we two servers. One is running 10.8.5, the other 10.9.1. Let me explain why. Apple has this "brilliant" idea that you can't create an older system restore on a newer system. The way around this is to make an older image on an older server, and then transfer the NetBoot file (we will get to these later) to the newer server. I tried this, and had mixed results. So keeping them separate is the way to go.

Servers can be shared on the same subnet. That shouldn't cause any problems, but for convenience purposes I suggest creating another subnet and running the servers on two separate subnets.

### Setup NetInstall

1. Get a Mac for a server if you don’t already have one. We are using a mac mini last I checked, we could do better and more powerful in the future.
2. Install Mavericks, or what ever the latest OS is. 
3. After installing OS X, go to the App Store and download the Server App provided by Apple. Will cost you $20, which ain't too bad.
4. Run the server app. On first time setup, it will walk you through what it wants. Create an admin account and such, and select this Mac as the server, not an extension of it (you can do that later if you want multiple servers for load balance).
6. Go to NetInstall and turn it on.

You now have a working NetInstall Server. However, you still can’t diagnose things. You will need to log onto GSX (Apple’s online website for authorized people and companies). More on that later.

## Imaging Explained

Here is where the process gets tricky. NetInstall allows OS X machines to boot over the network and onto the server for new images of an OS. From my understanding, here is the differences between the following options. You have three: NetBoot, NetInstall, and NetRestore.

* NetBoot: Allows you to boot into an image of OS X to run over the server to a machine. Pretty much allowing your machine to be a slave and run off the server. Good if the internal hard drive of the machine you are dealing with can't boot at all.
* NetInstall: Allows the machine to boot into a standard OS X installer, or a customized one if you want to do that. Note that, in this instance, if you have custom packages you want to install that have serial numbers wrapped in, this is not a good choice. The packaging app will strip your custom installation package of those scripts.
* NetRestore: Here is where things get cool. From my research, earlier models of OS X (like, pre Lion) required you to be model specific (e.g. an iBook image can’t be installed on a Macbook, and a G4 image can’t be installed on a G5). I read somewhere online that the later models could be used for earlier models, but I can’t confirm this. Now, as of Lion the image is very generic, and should, in theory, allow you to image from one computer to another with no problem. This is useful because you can create an image that has all your software on it.
Here’s the catch: it requires a recovery partition. What does this mean? It means we need a separate computer that can be reimaged and used to produce new images. This means that every time an image needs updating, you take your spare computer and update it, then capture a new image. I have read online that OS X can apply updates to existing images.


And finally, the last most important thing I have found: from my understanding, **you can only create images that are of the same OS system as the server**. That means Mavericks can only create Mavericks, Mountain Lion for Mountain Lion, etc. This is a huge pain because of the amount of work that will go into it.

## Apple Service Toolkit (AST)
Before using AST, we use to use ASD (Apple Service Diagnostics). These were system specific images distributed by Apple that ran full scans on the specific computer model we wanted. The problem with this is that it took us upwards of 40 minutes just to burn one of these to a flash drive to diagnose one computer. We don’t have that type of time, and we didn’t have a ton of flash drives, so we ended up reburning over and over again . Inefficiency is bad. This is not to say ASD is useless. I would recommend using it on systems that can’t do AST.

*Why AST?*: AST take exponentially less time. You boot over the network from the computer you plan on using. It will show you the diagnosis related to your computer. It has a GUI interface, so use the mouse and pick what you want.

* MRI; The most generic one is MRI. This does a full hardware test, and grabs system info like serial numbers and things while you are at it. It takes between 1-3 minutes

* Pixel test: This tests the screen for dead pixels

Those are the ones that show up on most Macs. Newer Macs, meaning ones using retina displays, have a screen image retention test.

* Retention Test: By requirement, and from what I have been told by a Genius, you **CANNOT** look at the screen while this test is running. The image is a black and white checkboard, and your brain is stupid and will remember the image for longer, even if not retained. You have to look at the result (which takes about 5 minutes to complete) to determine if there is an issue.

Now, the rest of the tests are pretty self explanatory, so I won’t go into them here. The last thing I will say is that you can also boot into our NetInstalls from this system, which is quite nice.

##### Running on Pre-2011 Mac:
From my understanding, AST only supports from 2011 up for specific hardware tests. Anything before then won’t see the netboot images. However, here are your options with pre-2011 macs:

1. Hold N on boot and run MRI
2. Burn an ASD image and run the test that way (although MRI is way faster)

##### AST SETUP:
1. Go to GSX. If you have acces to Jeremy Cole’s account (since that is the one I used originally) use it because it has all the important articles bookmarked.
2. Got to “Reference” section. Looks like a bunch of books
3. Search for “Apple Service Toolkit”. I kind of just found my way to it, so I can’t say I have an exact science to finding it.
4. Download Gateway Manager
5. Download ALL of the available ASTs and their parts
6. Setup Gateway Manager. It will ask you for credentials. 
7. Follow the instructions on Apple setup guide to properly enter the required info from GSX so you can activate Gateway Manager. IF YOU DON’T DO THIS, IT WON’T WORK.
8. Gateway manager can tell you what ASTs are installed, and what needs updating, so please check it every month or so and stay updated. On install, it will setup the things required to have AST show up on the network. Every time you install a new AST, it will be added automatically to the bootable files.