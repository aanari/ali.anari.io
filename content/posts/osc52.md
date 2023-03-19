---
title: "Yanking Text with OSC52"
date: 2022-08-02
draft: false
description: "Also works over SSH and tmux"
tags: [linux, shell]
---

## Overview

If your workflow involves connecting to a remote machine and developing on that environment, you might have grown frustrated with the lack of clipboard synchronization out of the box. This is a frequent need when working inside of a terminal emulator, as you will be constantly copying text between the terminal session and other applications (logs, test output, code snippets, etc).

You *could* solve this using [X11 forwarding](https://tldp.org/HOWTO/XDMCP-HOWTO/ssh.html); however the solution is brittle and also introduces quite a bit of latency. **OSC52** is an ANSI escape sequence that allows you to copy text into your system clipboard from anywhere, including from remote SSH sessions.

OSC stands for *Operating System Command*, a category of [ANSI escape sequences](https://en.wikipedia.org/wiki/ANSI_escape_code#Escape_sequences) which instruct the terminal emulator to perform certain actions.

OSC52 is one of these sequence that tells the terminal that the string it carries must be copied to the system clipboard. Typically, an application encodes a string in base64, prefixes it with the OSC52 sequence and outputs it. The terminal parses the OSC sequence and updates the clipboard.

The beauty of this approach is that works through arbitrary layers of nesting (SSH, tmux, screen). For example, the following configuration would still yank perfectly:

```
┌────────┐    ┌────────┐     ┌────────────┐    ┌──────┐    ┌────────────┐    ┌──────┐
│ client ├───►│ screen ├────►│ a.host.com ├───►│ tmux ├───►│ b.host.com ├───►│ tmux │
└────────┘    └────────┘     └────────────┘    └──────┘    └────────────┘    └──────┘
```

## Usage

Grab the OSC52 plugin for the editor that you use here:

* [Emacs](https://github.com/spudlyo/clipetty/blob/master/clipetty.el)
* [Neovim](https://github.com/ojroques/nvim-osc52)
* [Vim](https://github.com/ojroques/vim-oscyank)

## Limitations

Although almost all terminal emulators support OSC52, [Gnome Terminal](https://gitlab.gnome.org/GNOME/vte/-/issues/2495) still does not yet.

There is also a max copy limit of 74,994 bytes: The maximum length of an OSC52 escape sequence is 100,000 bytes. Subtracting the header/footer and accounting for the remaining bytes to contain base64-encoded text means that you’re left with 74,994 bytes. That may be plenty for your use case, but it really depends on your workflow.

