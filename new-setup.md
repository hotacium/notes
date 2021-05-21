
# Regolith OS での環境構築

## Regolith の採用理由

1. 普段使いするので安定性が必要 -> Arch よりも Ubuntu
2. タイルのウィンドウマネージャーが使いたい -> Regolith
3. 重くなってきたので OS 再インストールで軽量化する.

## Regolith のインストール

live USB をつくってインストールした. ここは Ubuntu と全く同じなので他のサイトに譲る

# Regolith の設定

## 日本語入力を可能にする

## インストール
- neovim
- python
- rust
- go
- binutils
- buildutils
- fish
- git
- curl
- build-essential

## fish

zsh と違っていろいろ簡単

シェルのヘビーユーザーではないのでこれで十分

- [fish](https://fishshell.com/)
- [oh-my-fish](https://github.com/oh-my-fish/oh-my-fish)


[themes](https://github.com/oh-my-fish/oh-my-fish/blob/master/docs/Themes.md) から良いテーマを探して、
```sh
$ omf install [theme]
```
でインストールして、

永続化させたい場合は `~/.config/fish/config.fish` に
```
set fish_theme [theme]
```
で OK.


## picom で背景透過

[config files](https://regolith-linux.org/docs/reference/configurations/)

でデフォルトのコンフィグファイルをコピペ.

## rust をインストール
公式から

## alacritty をインストール
gnome shell で背景透過がうまく行かなかったので、使ってみたかった Rust 製のターミナルエミュレータ alacritty を導入.

[インストール](https://github.com/alacritty/alacritty/blob/master/INSTALL.md) に従えばインストール可能.

依存プログラムをインストールしてから本体をインストール

デフォルトのシェルにするには以下を参照
- [set alacritty default](https://gist.github.com/aanari/08ca93d84e57faad275c7f74a23975e6)


## neovim-remote を導入
[neovim-remote installation](https://github.com/mhinz/neovim-remote/blob/master/INSTALLATION.md)

これを使うと、ウィンドウA で開いている neovim にウィンドウB の terminal からファイルを開くことができて便利

## 1password をインストール

公式から .deb をダウンロードして、

**ダウンロードしたディレクトリで** 次を実行
```sh
sudo apt install ./your-1password-package.deb
```


## 
