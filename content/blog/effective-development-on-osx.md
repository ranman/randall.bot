+++
author = "Randall Hunt"
date = 2014-03-23T00:00:00Z
description = ""
draft = false
slug = "effective-development-on-osx"
title = "Effective Development On A Mac"

+++

A lot of developers use Macs these days because it's the hot new thing. Unfortunately Macs are not the greatest development platform in the world out of the box. You need to do just a little bit of work to take your fancy new Macbook and turn it into a lean, mean, bug-crushing machine.


# The Tools

1. [Homebrew](http://brew.sh/): OS X's missing package manager
1. [iTerm](http://www.iterm2.com/): A better and more powerful terminal
1. [Slate](https://github.com/jigish/slate): or another window manager


## Homebrew

[Homebrew](http://brew.sh/) is the missing package manager of OS X. It's drastically better and more actively developed than its two competitors [fink](http://www.finkproject.org/) and [ports](http://www.macports.org/). If you are coming from a Linux world you can think of brew as `yum`, `apt-get`, `rpm`, or `emerge`.

To facilitate its installation we're going to update our path to make sure that `/usr/local/bin` is always ahead of `/usr/bin`. This means the shell will look for homebrew installed packages before looking for the system packages. This is desirable because OS X's system packages are often old or frustratingly modified. It also gives us a happy sandbox to workin that won't bork our system!

So open your `~/.SHELLrc` where SHELL is bash, zsh, etc. and change the path like so:

`export PATH=/usr/local/bin:$PATH`

Simple right?

Make sure you're not exporting the path a whole bunch of times (might have happened if you've had the machine for a while and installed some silly things).

Now we just install homebrew as documented here: http://brew.sh/#install

We can then install a package as simply as: `brew install python`.

We can see what options a package exposes with `brew info python`.

## iTerm

This one is easy to do. [Download](http://www.iterm2.com/#/section/downloads).

I find it useful to set a global hotkey for the terminal and I typically use `CMD+~` because I have a habit of using that hotkey from years of videogames.

Have a link or path in the terminal you want to open in finder or your browser? CMD+Click will do that. Autocompletion for everything in any context... these are just a few of the many reasons for using iTerm. I'll Just leave this link here for more [highlights](http://www.iterm2.com/#/section/documentation/highlights).



You can also open anything with whatever OS X thinks the default tool is with the `open` command.

## Slate

Do you frequently use your mouse to move windows around? Stop it. Every time you do that you're wasting time trying to get the OS to do something that it should be smart enough to do on its own.

With slate you can move windows around like a pro. Tile 3 windows next to each other? One key combo. Use one quarter of the screen? Done. Center a window and make it a certain size? Done.

Slate can be very complicated to get moving with though so I'd also recommend  [sizeup](https://www.irradiatedsoftware.com/sizeup/), [divvy](https://mizage.com/divvy/), [Spectacle](http://spectacleapp.com/), and [shiftit](https://github.com/fikovnik/ShiftIt).

To get you up and running with slate quickly checkout my dotfiles: https://github.com/ranman/dotfiles/blob/master/.slate

To move a window:

* left-half: `CMD+ALT+CTRL+LEFT`
* right-half: `CMD+ALT+CTRL+RIGHT`
* top-half: `CMD+ALT+CTRL+UP`
* bottom-half: `CMD+ALT+CTRL+DOWN`
* left-top-quarter: `SHIFT+CTRL+ALT+LEFT`
* left-bottom-quarter: `SHIFT+CTRL+ALT+DOWN`
* right-top-quarter: `SHIFT+CTRL+ALT+UP`
* right-bottom-quarter: `SHIFT+CTRL+ALT+RIGHT`

`CMD+E` will bring up a task switcher and `CMD+SHIFT+G` will bring up a window drawer.

## Other Tools

What I've outlined above is enough to save you cumulative hours a week. If you'd like to go further here are a few tools I find speed my development:

* [sublime](http://www.sublimetext.com/3): a great and full featured editor (get package control!), almost as good as vim.
* [silver-searcher](http://geoff.greer.fm/2011/12/27/the-silver-searcher-better-than-ack/): a better ack that's aware of your sourcecode.
* [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh): ZSH is a cool shell and oh-my-zsh has a lot of nice plugins.
* [alfred](http://www.alfredapp.com/): indispensable application launcher and multi-tool. It also can store your clipboard history!
* [textual](http://www.codeux.com/textual/): IRC client.
* [adium](https://adium.im/): IM client.
* [ipython](http://ipython.org/notebook.html): iPython is a great python repl and the notebook feature is indispensable.
* [dash](http://kapeli.com/dash): an offline documentation browser, great for a developer on the go.

I've also collected a well documented list of OSX hacks over the years that make things a lot easier (key repeat rate, autocorrect, etc.). You can look at all of those here: https://github.com/ranman/dotfiles/blob/master/.osx

