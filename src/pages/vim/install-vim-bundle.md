---
layout: ../../layouts/index.astro
---

# Vim のプラグイン管理

Vundle でプラグイン管理していく。

## インストール

```bash
git clone git@github.com:VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

`.vimrc` に以下を追記する。

```vim
set nocompatible      " be improved, required
filetype off          " required

set  rtp+=~/.vim/bundle/Vundle.vim

" Plugins
call vundle#begin()
Plugin 'VundleVim/Vundle.vim'
Plugin 'flazz/vim-colorschemes'
call vundle#end()
filetype plugin indent on
```

`:source%` とし、`:PluginInstall` とすると記載したプラグインがインストールされる。

- リスト内のインストール - `:PluginInstall`
- リスト内のプラグインを更新 - `:PluginUpdate`
- リストにないプラグインを削除 - `:PluginClean`

## 参考

[VundleVim/Vundle.vim: Vundle, the plug-in manager for Vim](https://github.com/VundleVim/Vundle.vim)
