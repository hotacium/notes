---
title: "Git のメモ"
description: "submodule, .gitignore"
date: 2021-06-12
tags: ["Git"]

---

Git で調べたことをメモしておく場所

## サブディレクトリをサブモジュール化する

1. 新しいレポジトリを作る
```sh
cd /path/to/git/repos/
mkdir Library1.git
cd Library1.git
git init --bare
```
2. プロジェクトのレポジトリをクローンする
```sh
git clone git@REMOTE_URL:/path/to/git/repos/Project.git
```
3. サブディレクトリのみを残す
```sh
git filter-branch --subdirectory-filter src/libs/Library1 -- --all
```
4. `origin` を書き換える
クローンしてきた `origin` があるので, それを新しいリモートレポジトリで置き換える
```sh
git remote rm origin
git remote add origin git@REMOTE_URL:/path/to/git/repos/Library1.git
```
5. 新しいリモートリポジトリに push する
```sh
git push origin master
```
6. 古いレポジトリのサブディレクトリを削除する
```sh
git rm -r src/libs/Library1/
git commit -m "Removed Library1 directory to replace it with submodule."
```
7. サブモジュールを追加する
```sh
git submodule add git@REMOTE_URL:/path/to/git/repos/Library1.git src/libs/Library1
```
8. commit する
```sh
git commit -m "Replaced Library1 directory with submodule."
git push origin master
```

### 参考
- [How to create git submodule from repository subdirectory](http://blog.davidecoppola.com/2015/02/how-to-create-git-submodule-from-repository-subdirectory/)




## .gitignore 芸 (?)
無視するファイルを指定するのではなく, Git に取り込むファイルを指定する. 

### 例: ドットファイルを Git 管理するときの `.gitignore`
ドットファイルは `.config` 下の設定ファイルや, `.bashrc`, `.vimrc` といった設定ファイル群

環境構築時に便利なので, Git 管理して GitHub 等においておきたい. しかし,
- すべての設定ファイルを Git に入れるのは怖い
- `.config` 下にはいらない設定ファイルもある
という事情から, Git に含めるファイルを個別に選択したい.


例: `./nvim/init.vim` のみを Git 管理したい場合.
```gitignore
# `./nvim/init.vim` のみを Git に入れたい

# 現在のディレクトリのファイル/ディレクトリを無視する 
# (`/*` でなく `*` とすると, サブディレクトリまで無視される)
/*
# `/nvim/` ディレクトリを含める
!/nvim/
# `/nvim/` 以下のファイルを無視する
/nvim/*
# `/nvim/init.vim` を含める
!/nvim/init.vim

```

実際の [Hmiya6/dotfiles](https://github.com/Hmiya6/dotfiles) 下の `.gitignore`:

```gitignore
# ignore all
/*

# not ignore `nvim/init.vim`
!/nvim/
/nvim/*
!/nvim/init.vim

# not ignore `fish/config.fish`
!/fish/
/fish/*
!/fish/config.fish

# not ignore `regolith/Xresources`
!/regolith/
/regolith/*
!/regolith/Xresources

# not ignore `regolith/picom/config`
!/regolith/picom/
/regolith/picom/*
!/regolith/picom/config

# not ignore `regolith/i3xrocks/conf.d/*`
!/regolith/i3xrocks/
/regolith/i3xrocks/*
!/regolith/i3xrocks/conf.d/


##########################################
# Example
##########################################
# to commit only `./nvim/init.vim`
# /* # ignore all files (just `*` contains all subdirectories)
# !/nvim/ # dont ignore `./nvim/`
# /nvim/* # ignore all files in `./nvim/`
# !/nvim/init.vim # but dont ignore `./nvim/init.vim`
```

### 参考
- [あるディレクトリ以下の全ファイルを, 特定のファイルだけ除いて無視したい](https://qiita.com/anqooqie/items/110957797b3d5280c44f#%E3%81%82%E3%82%8B%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%88%E3%83%AA%E4%BB%A5%E4%B8%8B%E3%81%AE%E5%85%A8%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%82%92%E7%89%B9%E5%AE%9A%E3%81%AE%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%A0%E3%81%91%E9%99%A4%E3%81%84%E3%81%A6%E7%84%A1%E8%A6%96%E3%81%97%E3%81%9F%E3%81%84)

