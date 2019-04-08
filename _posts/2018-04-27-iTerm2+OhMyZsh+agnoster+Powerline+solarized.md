---
layout: post_layout
title: Mac 终端配置
time: 2018年04月27日
location: 上海
published: true
excerpt_separator: '```'
---

## iTerm2

官网地址：http://iterm2.com/

## zsh

homebrew 安装 zsh  
brew install zsh zsh-completions

## oh my zsh

Oh My Zsh 是基于 zsh 的一个扩展工具集，它提供了丰富的扩展功能

via curl

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

via wget

```
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

## agnoster

可以看看它的默认主题：

ls ~/.oh-my-zsh/themes

需要修改主题只需：

```
vim ~/.zshrc
```

然后把里面 ZSH_THEME 的值改为 ZSH_THEME="agnoster"，保存退出

## powerline

```
git clone git@github.com:powerline/fonts.git
cd fonts
./install.sh
```

然后到 iterm2 配置，设置字体为 Roboto Mono for Powerline

## vim config

- git 下 Solarized 的源码
  git clone git://github.com/altercation/solarized.git

- 进入文件夹
  cd solarized/vim-colors-solarized/colors

- 下面可能要管理员权限
  sudo mkdir -p ~/.vim/colors
  sudo cp solarized.vim ~/.vim/colors/

- 创建.vimrc 文件
  sudo vim ~/.vimrc

- 把下面这三行复制进去
  syntax enable
  set background=dark
  colorscheme solarized
