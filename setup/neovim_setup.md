---
title: "Neovim Setup"
description: "Neovim の設定やプラグインなどのメモ"
date: 2020-02-10
tags: ["Vim"]
---

## Neovim Installation
From apt package manager
```sh
sudo apt install neovim -y
```

Config file path: `~/.config/nvim/init.vim`

## Neovim Config
Keep clean
```vim
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

" Encoding
set encoding=utf-8

" highlight the current line
set cursorline

" key mapping
imap ( ()<left>
imap [ []<left>
imap { {}<left>
```

## Dein.vim
I installed **dein.vim** to manage vim plugins

Link: [dein.vim](https://github.com/Shougo/dein.vim)

#### Install dein.vim
```sh
curl https://raw.githubusercontent.com/Shougo/dein.vim/master/bin/installer.sh > installer.sh
# For example, we just use `~/.cache/dein` as installation directory
sh ./installer.sh ~/.cache/dein
```

#### Edit init.vim (.vimrc)
1. Copy needed vim script from stdout when you executed the above shell script. Or copy the following:
```vim
if &compatible
  set nocompatible
endif
" Add the dein installation directory into runtimepath
set runtimepath+=~/.cache/dein/repos/github.com/Shougo/dein.vim

if dein#load_state('~/.cache/dein')
  call dein#begin('~/.cache/dein')

	" Add other plugins here
	" Example: call dein#add('github/path')
	""""""""""""""""""""""""""""""""

	""""""""""""""""""""""""""""""""
  call dein#end()
  call dein#save_state()
endif

filetype plugin indent on
syntax enable
colorscheme your-colorscheme
```
2. Paste it on your `init.vim` (or `.vimrc`)

3. Open vim, and `call dein#install()`
```vim
:call dein#install()
```

## Vim-Polyglot
> A collection of language packs for Vim.

This plugin is for colorscheme

Link: [vim-polyglot](https://github.com/sheerun/vim-polyglot)

#### Installation
Add the following to dein.vim script:
```vim
call dein#add('sheerun/vim-polyglot')
```

## Colorschemes
Links:
* [Edge](https://github.com/sainnhe/edge)
* [Spaceduck](https://github.com/pineapplegiant/spaceduck)
* [Vim-monokai](https://github.com/sickill/vim-monokai)

#### Installation
```vim
" edge
call dein#add('sainnhe/edge')

" spaceduck
call dein#add('pineapplegiant/spaceduck')

" vim-monokai
call dein#add('sickill/vim-monokai')
```

#### Configuing `colorscheme`
And then, **append** `colorscheme` to dein script on your `init.vim`
```vim
" edge
colorscheme edge
```
```vim
" spacedcuk
colorscheme spaceduck
```
```vim
" vim-monokai
colorscheme vim-monokai
```
Example:
```vim
" dein script ...
syntax enable
" put colorscheme here
colorscheme edge
```

## COC.nvim
COC.nvim hosts language servers

#### Installation
1. Install nodejs
```sh
curl -sL install-node.now.sh/lts | sh
```

2. Add coc.nvim to dein.vim
```vim
call dein#add('neoclide/coc.nvim')
```

3. Reload dein.vim
```vim
:call dein#install()
```

#### Configuration 
1. Install coc extension
* Rust language server: `coc-rls`
* Go language server: `coc-go`
* Python language server: `coc-python`
* C/C++ language server: `coc-ccls`
Other language servers: [here](https://github.com/neoclide/coc.nvim/wiki/Language-servers#register-custom-language-servers)

2. Install extensions
```vim
:CocInstall coc-rls coc-go coc-python coc-ccls
```

3. Configue `init.vim` (`.vimrc`)

Essential 
```vim
" TextEdit might fail if hidden is not set
set hidden

" Use tab for trigger completion with characters ahead and navigate.
" NOTE: Use command ':verbose imap <tab>' to make sure tab is not mapped by
" other plugin before putting this into your config.
inoremap <silent><expr> <TAB>
      \ pumvisible() ? "\<C-n>" :
      \ <SID>check_back_space() ? "\<TAB>" :
      \ coc#refresh()
inoremap <expr><S-TAB> pumvisible() ? "\<C-p>" : "\<C-h>"

function! s:check_back_space() abort
  let col = col('.') - 1
  return !col || getline('.')[col - 1]  =~# '\s'
endfunction


" Use K to show documentation in preview window.
nnoremap <silent> K :call <SID>show_documentation()<CR>

function! s:show_documentation()
  if (index(['vim','help'], &filetype) >= 0)
    execute 'h '.expand('<cword>')
  elseif (coc#rpc#ready())
    call CocActionAsync('doHover')
  else
    execute '!' . &keywordprg . " " . expand('<cword>')
  endif
endfunction
```

More configuration: [here](https://github.com/neoclide/coc.nvim#example-vim-configuration)


## `init.vim`
ver 2021-02-21
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

" Encoding
set encoding=utf-8

" highlight the current line
set cursorline

"""""""""""""""""""""""""""""""""""""""""
" Key Mapping
"""""""""""""""""""""""""""""""""""""""""
imap ( ()<left>
imap [ []<left>
imap { {}<left>
" imap < <><left>

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
  call dein#add('vim-airline/vim-airline')
  call dein#add('vim-airline/vim-airline-themes')
  "call dein#add('rust-lang/rust.vim')

  " Required:
  call dein#end()
  call dein#save_state()
endif

" Required:
filetype plugin indent on
syntax enable
colorscheme edge

""""""""""""""""""""""""""""""""""""""""
" Coc.nvim Settings
""""""""""""""""""""""""""""""""""""""""

" TextEdit might fail if hidden is not set
set hidden

" Use tab for trigger completion with characters ahead and navigate.
" NOTE: Use command ':verbose imap <tab>' to make sure tab is not mapped by
" other plugin before putting this into your config.
inoremap <silent><expr> <TAB>
      \ pumvisible() ? "\<C-n>" :
      \ <SID>check_back_space() ? "\<TAB>" :
      \ coc#refresh()
inoremap <expr><S-TAB> pumvisible() ? "\<C-p>" : "\<C-h>"

function! s:check_back_space() abort
  let col = col('.') - 1
  return !col || getline('.')[col - 1]  =~# '\s'
endfunction


" Use K to show documentation in preview window.
nnoremap <silent> K :call <SID>show_documentation()<CR>

function! s:show_documentation()
  if (index(['vim','help'], &filetype) >= 0)
    execute 'h '.expand('<cword>')
  elseif (coc#rpc#ready())
    call CocActionAsync('doHover')
  else
    execute '!' . &keywordprg . " " . expand('<cword>')
  endif
endfunction

"""""""""""""""""""""""""""""""""""""""
" Vim Airline Themes
"""""""""""""""""""""""""""""""""""""""
" let g:airline_theme = 'ayu_mirage'
" let g:airline_theme = 'badwolf'
let g:airline_theme = 'hybridline'
