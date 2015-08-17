---
layout: post
title: "Setting up No-IP with Freenas"
published: true
---


I've had my Freenas server for almost a year now, but while away at school I can only SSH into the server if I know what my IP address is for my home router. Main problem? The public IP is changed every few months, leaving me with no way of knowing what my IP will be in the future. There are a multitude of ways to pay for these things, but I'm doing it on the cheap because I don't have money or care for a subscription service. So here is what you will need:

A router that supports DynDNS (I'm using the Actiontec router Verizon gave me)
A server running Freenas
A noip.com account
I'm going to take into assumption here that you have basic networking knowledge and know how to setup all these without me. I'm just going into how you can always access your Freenas server from anywhere outside of your home. Let's get started.

-----

## Setting up No-IP

Start by creating your account. Once you have done that you want to go to "Manage DNS" on the top bar. You will be presented with an empty table asking if you want to add a new host. Click "Add host".

![add_host.png]({{site.baseurl}}/_posts/add_host.png)

Here you want to fill out your Hostname. I suggest something easy to remember, like freenas-yourname.ddns.net. You will need this later, so remember it. The ending to the hostname doesn't matter. It can be what ever you want from that drop down list. Leave the host type as DNS Host. Your IP address should be pre filled, if not you can go to whatsmyip.org to find out what your public IP is. After that, just go ahead and hit save. That's all we need to worry about here.

