## iTerm2

官网地址：http://iterm2.com/

## zsh

homebrew 安装zsh  
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
可以看看它的默认主题：

ls ~/.oh-my-zsh/themes

需要修改主题只需：

```
vim ~/.zshrc
```

然后把里面 ZSH_THEME 的值改为 ZSH_THEME="agnoster"，保存退出

## Powerline

```
git clone git@github.com:powerline/fonts.git
cd fonts
./install.sh
```
然后到 iterm2 配置，设置字体为Roboto Mono for Powerline
