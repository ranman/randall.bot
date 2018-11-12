+++
author = "Randall Hunt"
date = 0001-01-01T00:00:00Z
description = ""
draft = true
slug = "hackathon-knowledge-your-environment"
title = "Hackathon Knowledge: Improve Your Developer Environment"

+++

![Don't Be This Guy](/content/images/2014/Sep/blog_post_computer_hate.jpg)

Often overlooked but incredibly important to be able to code quickly and reliably is a good environment. What do I mean by a good environment? There are three main components: package management, editors and linting, and dependency management. Having a good environment can save you hours when it comes to getting ready to use a new technology or framework. It can also make it easy to move things from one computer to another. Maintaining your environment is essential to spending more time coding and less time fighting with paths and package names. In this post I'll cover the package management segment of your environment. In later posts I'll address editors, linting, and dependency management.

###Package Management

####Linux
Linux distributions typically come with pretty great package management (if not from a security standpoint then at least from an accesiblity standpoint). You've got tools like `apt-get`,`emerge`,`yum`, and a few hundred others. I don't know what kind of setup you're rocking on linux so I'm not going to tell you how to run with things but these are a few tips that have helped me over the years:

1. Prefer the vendor's repos over the distro's repos. This means for something like [MongoDB](http://docs.mongodb.org/) you're going to want to follow a tutorial like this one: "[Install MongoDB on Red Hat Enterprise, CentOS, Fedora, or Amazon Linux](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-red-hat-centos-or-fedora-linux/)" for yum/rpm based distros or this one "[Install MongoDB on Ubuntu](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/)" for ubuntu/debian based distros. This means you can use the most up to date package without relying on your distro to ship it to you. There are obviously exceptions to this for the package managers that build from source.
2. With `yum` just about everything you can imagine can be found with the following command `yum --enablerepo=epel install` followed by the package you were looking for.

####OSX
OSX is unlucky in that you'll have to download the pretty hefty developer tools before you can get going. Try to do this on a good connection. There are a lot of different ways to do it. The general steps to get moving quickly are:

1. Open a [terminal](http://iterm2.com/) and run `xcode-select --install` if it fails make sure you don't already have them set by running `xcode-select -p` and checking that path. Alternatively you can download all of [Xcode](https://itunes.apple.com/us/app/xcode/id497799835).
2. Next install homebrew. Get rid of any other package manager on your system.
3. Try to install as much as possible through homebrew. If a package does not exist consider making it! I also really recommend placing `/usr/local/bin` before `/usr/bin` in your path and upgrading the permissions to allow the staff group control over it: `sudo chown -R :staff /usr/local` and `sudo chmod -R g+wr /usr/local`.
4. If you do need to manually compile and install things consider putting them in /opt or ~/opt instead of the typical lib locations.
5. Install the most up to date language versions for python, ruby, node, etc. through homebrew and make sure you use those versions rather than the system provided defaults. This will solve a lot of problems related to dependency management.
6. You can search for a package in brew with `brew search vim` and get information on an individual package with `brew info postgresql`.
7. Don't use `launchctl` to run services because you'll probably forget about where those things live and where they are. `launchd` can be pretty good for running cron jobs though, just remember to keep track.


####Windows
Windows is probably the worst off for the whole bunch for doing traditional dev. On the plus side it's fantastically well setup for .NET development so if that's your forte then you'll be up and running in no time. Windows is also the environment I have the least experience with so please take this advice with a ~~grain~~ boulder of salt. If you have any suggestions please let me know and I'll incorperate them.
1. [Choclatey](https://chocolatey.org/) the homebrew of windows.
2. [NuGet](https://www.nuget.org/) package manager for .NET.
3. [Rapid Environment Editor](http://www.rapidee.com/en/about) gives you a GUI for changing 

####General
1. Create a location for custom binaries to live. On linux and osx this can be as simple as `~/bin`
2. If you have to configure and install a package manually make sure you keep a record of it in a text file somewhere. This really helps for tracking down dependency problems later on. You might have this harddrive for years and you'll most likely forget large portions of the custom packages you install.
3. Don't be afraid to modify the environment, just make sure you know what you're doing and keep a record.
4. Figure out a good way to maintain your actual `env`: path variables, JAVA_HOME, and other variables that might need changing but need sensible defaults. I'll outline how I manage this on different operating systems in another post.

