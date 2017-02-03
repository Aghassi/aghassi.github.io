---
layout: post
title: "Scripting the setup of your new Mac."
published: false
---

Who likes doing clean installs? Aside from the supposed performance benefits and getting a clean slate, it is a real hassle. If you are a developer, it is even more of a hassle if you have things like `rc` files and other dotfiles. Plus, you have to go find all your favorite apps all over again, which would not be a huge deal if the Mac App Store wasn't terrible.

I recently spent some times rewriting my new Mac setup script in anticipation for the inevitable time in the future that I would use it (turned out not to be that soon). You can find the finished product of all I'm about to talk about in my repository called [dotfiles](https://github.com/Aghassi/dotfiles).

### Utilizing Package Managers
The key to all this is being comfortable with your terminal and some package managers to speed things up. There are two (well three really) managers that I used for this script.

  * `brew` - [brew.sh](http://brew.sh), allows us to install command line utilities, like `apt-get` on Linux.
  * `brew cask` - [caskroom](https://caskroom.github.io), allows for installing third party Mac software.
  * `mas` - [mas-cli](https://github.com/mas-cli/mas), allows us to install apps that you have purchased through the Mac App Store using the command line.

With these tools, we can do almost all of our initial setup. As an up front, I'm not saying this is perfect, or that there aren't better ways. This is the way I enjoy setting up my Mac because it allows me to do a "one and done" situation, and I have control over what I am installing and what I am setting up.

As a web developer, I have a heavy reliance on certain command line utilities. Namely, `node`, `nvm`, `npm`. So let's use `brew` to install those.

```bash
brew install node nvm npm
```

Awesome! So now we have
