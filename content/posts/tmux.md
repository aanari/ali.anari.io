---
title: "The 6 Best Tmux Plugins"
date: 2022-10-24
draft: false
description: "A quick plugin guide"
tags: [tmux, shell]
---

## Overview

For [tmux](https://github.com/tmux/tmux) users who are used to writing all their own bespoke configurations, [Tmux Plugin Manager](https://github.com/tmux-plugins/tpm) makes that a breeze now.

Plugins will drastically cut down the size of your config, and prevent you from making mistakes by copying and pasting random snippets until you find what works.

Once you have `tpm` installed, I also recommend that you bind a convenient key mapping to reload your `~/.tmux.conf`, so that you can make changes on the fly without having to constantly restart your tmux server to verify:

```
bind-key r source-file ~/.tmux.conf \; display-message "~/.tmux.conf reloaded"
```

## Useful Plugins

These were the most useful plugins for me, that stood the test of time.

* [tmux-better-mouse-mode](https://github.com/NHDaly/tmux-better-mouse-mode): Better mouse support, scrolling speed, emulation for unsupported programs, and more.
* [tmux-copycat](https://github.com/tmux-plugins/tmux-copycat): Regex searching and results highlighting, and predefined searches.
* [tmux-open](https://github.com/tmux-plugins/tmux-open): Opening highlighted selections directly from copy mode.
* [tmux-sensible](https://github.com/tmux-plugins/tmux-sensible): Self-explanatory. A set of tmux options that should be acceptable to everyone.
* [tmux-yank](https://github.com/tmux-plugins/tmux-yank): Copy to the system clipboard from tmux.
* [vim-tmux-nagivator](https://github.com//christoomey/vim-tmux-navigator): For vim users, allows you to navigate seamlessly between vim and tmux splits using a consistent set of hotkeys.


## Problematic Plugins

These are plugins that are well regarded by the developer community, and otherwise well engineered, but that I eventually removed from my config for various reasons.

* [tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect): Saves and restores all of your pane layouts and prompt output. However, programs were not restored as well. Ended up causing clutter on startup.
* [tmux-continuum](https://github.com/tmux-plugins/tmux-continuum): Supposed to automatically save your sessions so they can be restored by `tmux-resurrect`. However, this never worked reliably for me, so I had to manually save my layouts each time.
