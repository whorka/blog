---
layout: post
title: Running Steam on Linux (Fedora 15)
tags: linux games
---

# {{ page.title }}

I bought a couple of the [[link http://www.humblebundle.com/]]Humble Indie Bundles[[/link]] (mostly for [[link http://thelettervsixtim.es/]]VVVVVV[[/link]]) and was having great fun playing them on my Mac, but about half the games were inaccessible due to lack of Mac support, so I decided to see what the state of Steam on Linux was.

Turns out, gaming on Linux is a lot better than it was last time I looked, and while it's not officially supported, I got Steam up and running on Fedora 15 (yeah I'm a bit behind the times) and it just took a few steps, and no compilation!

Following https://developer.valvesoftware.com/wiki/Steam_under_Linux ...
* Install Play On Linux:
** rpm --import http://rpm.playonlinux.com/public.gpg
** wget -O /etc/yum.repos.d/playonlinux.repo http://rpm.playonlinux.com/playonlinux.repo
** yum install playonlinux
** Install wine-gecko per http://www.sysadminworld.com/2011/fix-wine-could-not-find-the-gecko-package-in-fedora-16/
** Gecko is a Mozilla build for Wine to replace Internet Explorer HTML rendering functionality.
** yum install cabextract
** wget -O install-gecko.sh http://winezeug.googlecode.com/svn/trunk/install-gecko.sh
** chmod +x install-gecko.sh
** ./install-gecko.sh
* Install Steam
** playonlinux
** Install -> Games -> Steam
** First time running wine, it will prompt to install fonts. Go ahead and install.
* (Optional) Install Winetricks:
** Winetricks automates installation of selected apps under Wine (much like Play on Linux or Autoplus) and does other tweaks too. There's an option for installing Steam here too, but I went with Play on Linux instead.
** wget -O /usr/bin/winetricks http://winetricks.org/winetricks
** chmod +x /usr/bin/winetricks
* Run Steam
** playonlinux
** Steam -> Run
** The Steam dialog windows (login/setup/install) have a weird tiling/duplication on my system (same window content repeated multiple times) but the main window looks fine after I log into Steam and maximize the window.
* Download and Play!
* (Extra Credit) Check http://www.steamgamesonlinux.com/ for compatibility.

