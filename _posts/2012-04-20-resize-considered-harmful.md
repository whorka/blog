---
layout: post
title: resize(1) Considered Harmful
tags: linux shell
---

# {{ page.title }}

I ran across this innocuous little line in a profile script while trying to debug why buffered input was discarded if received before login was completed:

    eval `/usr/bin/resize -u 2>/dev/null`

Turns out, resize(1) sends an escape sequence to terminal and reads return characters to get terminal size. This can mess up if something is buffered in STDIN so we can't run it at login (or probably even anytime that terminal I/O might be happening).

Instead, this works much better:

    eval `/bin/stty size |sed -e 's/^/LINES=/' -e 's/ /; COLUMNS=/' -e 's/$/; export LINES COLUMNS/'`

