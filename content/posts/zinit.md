---
title: "Supercharged ZSH with Zinit"
date: 2023-03-16
draft: false
description: "Easy configuration examples"
tags: [zsh, shell]
---

## Overview

If you are a [zsh](https://zsh.sourceforge.io/) user, you may have read about the performance issues with [oh-my-zsh](https://ohmyz.sh/), despite its robust community.

[zinit](https://github.com/zdharma-continuum/zinit) is a fast and flexible plugin ZSH manager that solves all of these issues elegantly, and can even install useful binaries for you.

To get started, you can follow either the manual or automatic installation guides on Zinit's GitHub Page, ensuring to call `source ~/.zinit/bin/zinit.zsh` somewhere in your `~/.zshrc`.

## Useful Plugins

Here is a list of some of the most useful plugins and customizations that I've found, for my day-to-day development needs.

### Annexes

Install and run Ruby gems, Node and Python modules. Download files and apply patches.

```bash
zinit light-mode for \
  zinit-zsh/z-a-bin-gem-node \
  zinit-zsh/z-a-patch-dl
```

### CLI Tools

Provides [enhancd](https://github.com/b4b4r07/enhancd), [lsd](https://github.com/lsd-rs/lsd), [bat](https://github.com/sharkdp/bat), and [exa](https://the.exa.website/).

```bash
zinit light b4b4r07/enhancd
zinit light Peltoche/lsd
zinit wait"1" lucid from"gh-r" as"null" for \
  sbin"**/bat"  @sharkdp/bat \
  sbin"**/exa"  ogham/exa
```

### FZF

Installs [fzf](https://github.com/junegunn/fzf) and [fzf-tab](https://github.com/Aloxaf/fzf-tab) with completion configuration, disabling sorting when completing `git checkout`.

```bash
zinit pack="bgn-binary+keys" for fzf
zinit light Aloxaf/fzf-tab
zstyle ':completion:*:git-checkout:*' sort false
# set descriptions format to enable group support
zstyle ':completion:*:descriptions' format '[%d]'
# set list-colors to enable filename colorizing
zstyle ':completion:*' list-colors ${(s.:.)LS_COLORS}
# preview directory's content with exa when completing cd
zstyle ':fzf-tab:complete:cd:*' fzf-preview 'exa -1 --color=always $realpath'
# switch group using `,` and `.`
zstyle ':fzf-tab:*' switch-group ',' '.'
```

### ZSH Core Functionality

General ZSH syntax highlighting, completions, and suggestions.

```bash
zinit wait lucid for \
  atinit"ZINIT[COMPINIT_OPTS]=-C; zicompinit; zicdreplay" \
     zdharma/fast-syntax-highlighting \
  blockf \
     zsh-users/zsh-completions \
  atload"!_zsh_autosuggest_start" \
     zsh-users/zsh-autosuggestions
```

### Z, Z tab completion

Configuration of the useful [z](https://github.com/rupa/z) utility.

```bash
zinit ice wait blockf lucid
zinit light rupa/z
zinit ice wait lucid
zinit light changyuheng/fz
```

### Clipboard

Utility for yanking.

```bash
zinit ice as"program" pick"yank" make
zinit light mptre/yank
```

### direnv

Unclutters your `~/.profile`.

```bash
# direnv
zinit as"program" make'!' atclone'./direnv hook zsh > zhook.zsh' \
  atpull'%atclone' pick"direnv" src"zhook.zsh" for \
  direnv/direnv
```

### pyenv

Simple Python version management.

```bash
# pyenv
zinit ice wait lucid depth'1' \
    atclone'PYENV_ROOT="${HOME}/.pyenv" ./libexec/pyenv init - > zpyenv.zsh' \
    atinit'export PYENV_ROOT="${HOME}/.pyenv"' atpull"%atclone" \
    as'command' pick'bin/pyenv' src"zpyenv.zsh" compile'{zpyenv,completions/*}.zsh' nocompile'!' \
    atload'
    eval "$(pyenv init --path)"
    eval "$(pyenv init -)"
    # make zsh completion works no need source in zshrc. #1644
    # https://github.com/pyenv/pyenv/pull/1644
    _pyenv_new() {
        local -a comples
        if [ "${#words}" -eq 2 ]; then
            comples=($(pyenv commands))
        else
            comples=($(pyenv completions ${words[2,-2]}))
        fi
        _describe -t comples 'comples' comples
    }
    compdef _pyenv_new pyenv' \
    id-as'pyenv'
zinit light pyenv/pyenv
zinit ice wait lucid depth'1' \
    atclone'PYENV_ROOT="${HOME}/.pyenv" ./bin/pyenv-virtualenv-init - > zpyenv-virtualenv.zsh' \
    atinit'export PYENV_ROOT="${HOME}/.pyenv"' atpull"%atclone" \
    as'command' pick'bin/*' src"zpyenv-virtualenv.zsh" compile'*.zsh' nocompile'!' \
    atload'
    eval "$(pyenv init -)"' \
    id-as'pyenv-virtualenv'
zinit light pyenv/pyenv-virtualenv
```

### poetry

Python packaging and dependency management made easy.

```bash
zinit ice pick'poetry.zsh'
zinit light sudosubin/zsh-poetry
```

### nvm

Simple Node.JS version management.

```bash
export NVM_COMPLETION=true
export NVM_SYMLINK_CURRENT="true"
zinit wait lucid light-mode for lukechilds/zsh-nvm
```
