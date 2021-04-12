---
title: "Setup memo"
date: 2021-02-06
description: "環境構築をするときに参照するメモ"
tags: ["Linux"]
---

neovim の環境構築は別につくります.  

## Super User Settings
#### 0. login as super user
```sh
sudo su -
```
#### 1. set new su password
```sh
passwd
```

## Installation With Package Manager
#### apt
* curl
* git
* zsh
* neovim
* gnome-shell-extensions
* gnome-tweaks
* build-essential
* go
* fcitx-mozc
* net-tools

#### snap
* code (classic)
* sublime-text (classic)
* chromium
* discord

## Neovim Setup
* [dein.vim](https://github.com/Shougo/dein.vim)
* [spaceduck](https://github.com/pineapplegiant/spaceduck)
* [vim-polyglot](https://github.com/sheerun/vim-polyglot)
* [edge](https://github.com/sainnhe/edge)
* [coc.nvim](https://github.com/neoclide) with [node.js](https://github.com/nodesource/distributions/blob/master/README.md#debinstall)

#### 0. Run below script
```sh
curl https://raw.githubusercontent.com/Shougo/dein.vim/master/bin/installer.sh > installer.sh
# For example, we just use `~/.cache/dein` as installation directory
sh ./installer.sh ~/.cache/dein
```

#### 1. Edit ~/.config/nvim/init.vim
create ~/.config/nvim
```sh
mkdir ~/.config/nvim
vim ~/.config/nvim/init.vim
```
edit init.vim
```vim
"""""""""""""""""""""""""""""""""""""""""
" Neovim Settings
"""""""""""""""""""""""""""""""""""""""""

" display index number
set number

" indent and tab
set autoindent
set tabstop=2
set shiftwidth=2
set expandtab
set list

" share clipboard
set hlsearch
set ignorecase
set smartcase
set incsearch

" display cursor position
set ruler

" enable mouse
set mouse=a

" enable syntax highlight
syntax on

"""""""""""""""""""""""""""""""""""""""""
" Key Mapping
"""""""""""""""""""""""""""""""""""""""""
imap ( ()<left>
imap [ []<left>
imap { {}<left>

"""""""""""""""""""""""""""""""""""""""""
" Dein.vim
"""""""""""""""""""""""""""""""""""""""""

if &compatible
  set nocompatible               " Be iMproved
endif

" Required:
set runtimepath+=/home/popondayu/.cache/dein/repos/github.com/Shougo/dein.vim

" Required:
if dein#load_state('/home/popondayu/.cache/dein')
  call dein#begin('/home/popondayu/.cache/dein')

  " Let dein manage dein
  " Required:
  call dein#add('/home/popondayu/.cache/dein/repos/github.com/Shougo/dein.vim')

  " Add or remove your plugins here like this:
  "call dein#add('Shougo/neosnippet.vim')
  "call dein#add('Shougo/neosnippet-snippets')
  "call dein#add('pineapplegiant/spaceduck')
  call dein#add('sheerun/vim-polyglot')
  call dein#add('sainnhe/edge')
  call dein#add('neoclide/coc.nvim')

  " Required:
  call dein#end()
  call dein#save_state()
endif

" Required:
filetype plugin indent on
syntax enable
colorscheme edge
```

#### 2. Open neovim and install dein
```vim
:call dein#install()
```

## Zsh Setup

#### 0. set zsh as login shell
0. get zsh path
```sh
$ cat /etc/shells
$ which zsh
```
1. set zsh
```sh
$ sudo chsh [username] -s [zsh path]
```
2. ***logout and login***

#### 1. Install prezto
[prezto - github](https://github.com/sorin-ionescu/prezto)

run below code
```sh
git clone --recursive https://github.com/sorin-ionescu/prezto.git "${ZDOTDIR:-$HOME}/.zprezto"
```

#### 2. Edit .zpreztorc
```sh
vim ~/.zpreztorc
```

#### 3. Edit .zshrc
```sh
vim ~/.zshrc
```
```.zshrc
############################################
# ALIAS
############################################

alias python='python3'
alias shutdown='systemctl poweroff'
alias reboot='systemctl'
alias pubip='curl inet-ip.info'
alias vpn='nordvpn'

############################################
# PATH
############################################
# export PATH=/foo/bar:$PATH

export PATH=/bin:$PATH
export PATH=/usr/bin:$PATH
export PATH=/usr/local/bin:$PATH
export PATH=$HOME/.local/bin:$PATH
```
then reload zsh:
```sh
source ~/.zshrc
```
## Tweaks Setup
#### 0. Turn "user themes" on
if you cannot, relogin again

#### 1. Logout and login

#### 2. Create ~/.themes ~/.icons
```sh
mkdir ~/.themes ~/.icons
```

#### 3. Download themes and icons
[Nordic darker](https://www.gnome-look.org/p/1267246/) -> ~/.themes
[Papirus dark](https://www.gnome-look.org/p/1166289/) -> ~/.icons
[Sweet cursors](https://www.gnome-look.org/p/1393084/) -> ~/.icons

## Mozc Setup
#### 0. Set input method as fcitx
Settings -> Region & Language -> Input Sources "Settings"  
-> Keyboard input method system: fcitx

#### 1. Remove ibus
```sh
sudo apt purge ibus
```
#### 2. Restart

#### 3. Add mozc to input method


## NordVPN Setup
Run the following command:
```sh
sh <(curl -sSf https://downloads.nordcdn.com/apps/linux/install.sh)
```

## Rust Installation
```sh
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## Go Installation
#### 1. Download the archive
From [here](https://golang.org/doc/install#download)

#### 2. Extract it into `/usr/local`
Run the following:
```sh
tar -C /usr/local -xzf go1.XX.X.linux-amd64.tar.gz
```

#### 3. Add `/usr/local/go/bin` to the PATH environment variable

Add the following to `.zshrc` (or `.bashrc`)
```sh
export PATH=$PATH:/usr/local/go/bin
```

#### 4. Verify that Go is installed
```sh
go version
```






