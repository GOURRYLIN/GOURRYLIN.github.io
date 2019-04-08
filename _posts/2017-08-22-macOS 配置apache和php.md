---
layout: post_layout
title: macOS 重新配置 apache 和 php
author: Rainloury
time: 2017年08月22日 星期二
location: 上海
published: true
excerpt_separator: '```'
---

## Step 1 关闭 System Integrity Protection

System Integrity Protection 苹果的**系统集成保护**，关闭 System Integrity Protection 获取系统的 rootless 权限后可任意操作系统的文件

- 查看 SIP 状态 terminal 运行 csrutil status，系统默认状态 enable。

- 重启电脑，按住 option,选择磁盘时，Command + R 进入系统 recovery 模式

- 菜单终端输入 csrutil disable，重启电脑。

- 想要恢复 SIP，再次进入 recovery 模式终端输入 csrutil enable 即可。

## Step 2 删除系统内置 apache 和 php

因系统内置的 apache 和 php 模块和依赖不全，更新麻烦且容易出问题，所以果断删除，此删除方法虽然不彻底，但已不影响重新安装和使用。

- 删除 apache

```
sudo apachectl stop
sudo rm -rf /etc/apache2
sudo rm -rf /usr/include/apahce2
sudo rm -rf /usr/libexec/apache2
```

- 删除 php

```
sudo rm -rf /usr/php
sudo rm -rf /usr/bin/php
sudo rm -rf /usr/bin/php-config
sudo rm -rf /usr/bin/phpize
sudo rm -rf /usr/include/php
sudo rm -rf /usr/lib/php
sudo rm -rf /usr/share/man/man*/php*
//php删除不是很彻底，还有一些依赖包
```

## Step 3 安装 Xcode 和 Homebrew

macOS 比较好用的包管理器有 Macports 和 Homebrew，两者各有利弊，在此不做赘述，选择 Homebrew。
安装 Xcode，因 Homebrew 是源码编译安装软件，安装 Xcode 内置了 command line tools,增添了编译所需的库，减少编译安装软件的错误。

```bash
//安装Homebrew
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
//检查安装是否成功，返回版本信息表示安装成功
$ brew --version
//检查配置是否无误,未返回error信息表示配置没有问题
$ brew doctor
```

## Step 4 安装 apache

- 添加 apache 软件仓库

```bash
brew tap homebrew/apache
```

- 更新软件仓库

```bash
brew update
```

- 安装 apache，apache 服务名为 httpd

```bash
brew install httpd24 --with-privileged-ports --with-http2
```

安装成功后会返回 apache 的目录路径，如： /usr/local/Cellar/httpd24/2.4.23_2: 212 files, 4.4M, built in 1 minute 45 seconds

- 重启 apache

```bash
sudo apachectl restart
```

此时浏览器输入 localhost 会看到 apache 的默认页面 “It works！”，apache 安装完成。

**Apache 配置**

- 修改配置文件

```bash
sudo vim /usr/local/etc/apache2/2.4/httpd.conf
```

- 修改 DocumentRoot 网站根目录

```bash
DocumentRoot "/usr/local/var/www/htdocs"
```

- 修改项目文件夹目录,与 DocumentRoot 保持一致

```bash
<Directory "/Users/your_user/sites">
```

- 配置 ServerName 你的网站名称，如果注释掉 ServerName，默认注释掉了，重启 apache 会报错。
- 修改用户和用户组，改为你的用户名 your_user 和 staff，避免访问权限问题。

```bash
User your_user
Group staff
```

- 开启 AllowOverride

```bash
# AllowOverride controls what directives may be placed in .htaccess files.
# It can be "All", "None", or any combination of the keywords:
#   AllowOverride FileInfo AuthConfig Limit
#
AllowOverride All
```

- 开启 url 的 rewrite 功能，开启此模块

```bash
LoadModule rewrite_module libexec/mod_rewrite.so
```

- 开启 apache 服务器反向代理模块

```bash
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_http_module modules/mod_proxy_http.so
```

## Step 5 安装 PHP

PHP 可同时安装多个版本 php5.4,php5.5,php5.6,php7.1，且能在不同版本之间切换,安装另一个版本前，先 brew unlink php 当前版本，然后 brew install php 版本号。通过 php -v 参看当前系统启用的 php 版本。

- 安装 php 的代码仓库

```bash
brew tap homebrew/php
```

- 更新软件仓库

```bash
brew update
```

- 安装 php

```bash
//--with-httpd24参数来编译安装 PHP 以及使 Apache 支持 PHP 所需要的一些模块
brew install php55 --with-httpd24
brew unlink php55
brew install php56 --with-httpd24
brew unlink php56
brew install php71 --with-httpd24
```

- 开启 apache 配置里对应的 php 版本模块，不需要的模块注释掉

```bash
sudo vim usr/local/etc/apache2/2.4/httpd.conf
```

```bash
LoadModule php5_module        /usr/local/Cellar/php55/5.5.38_11/libexec/apache2/libphp5.so
LoadModule php5_module        /usr/local/Cellar/php56/5.6.29_5/libexec/apache2/libphp5.so
LoadModule php7_module        /usr/local/Cellar/php71/7.1.0_11/libexec/apache2/libphp7.so
```

通过 brew unlink php 版本号 和 brew link php 版本号,来切换 php 版本,同时注释掉 apahce 配置中 unlink 的 php 模块和开启 link 对应的 php 模块。

**配置 PHP**

- 主目录索引文件配置

```bash
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

<FilesMatch .php$>
    SetHandler application/x-httpd-php
</FilesMatch>
```

- 验证 php 安装结果
  在根目录下新建 info.php,输入如下代码

```bash
<?php
phpinfo();
?>
```

浏览器访问：http://localhost/info.php ，此时能开到 php 初始化页面信息表示 php 安装完成。

## Step 6 配置虚拟主机

默认 apche 服务器只有一个网站根目录，可通过配置虚拟主机，同时控制多个站点。

```bash
//修改apache配置文件
sudo vim /usr/local/etc/apache2/2.4/httpd.conf
```

- 开启 apache 虚拟主机模块功能

```bash
LoadModule vhost_alias_module libexec/apache2/mod_vhost_alias.so
Include /private/etc/apache2/extra/httpd-vhosts.conf
```

- 修改虚拟主机配置文件,一个 VirtualHost 配置对应一个服务站点。

```bash
sudo vim /usr/local/etc/apache2/2.4/extra/httpd-vhosts.conf
```

```bash
<VirtualHost *:80>
        DocumentRoot "/Users/gourry/Documents/mysites/boxuegu"
        ServerName bxg.com
        ServerAlias www.bxg.com
        ErrorLog "/private/var/log/apache2/dummy-host2.example.com-error_log"
        CustomLog "/private/var/log/apache2/dummy-host2.example.com-access_log" common
        <Directory "/Users/gourry/Documents/mysites/boxuegu">

             # 是否来显示文件根目录的目录列表
             Options Indexes FollowSymLinks MultiViews
             MultiviewsMatch Any
             # 根目录下的.htaccess起rewrite作用,All or None
             AllowOverride All

             Order Allow,Deny
             Allow from all

             Require all granted

             # AllowOverride None 时设置，不希望根目录下有.htaccess文件时可把rewrite功能写进来
             # RewriteEngine on
                        # RewriteCond %{REQUEST_FILENAME} !-d
             # RewriteCond %{REQUEST_FILENAME} !-f
             # RewriteRule ^(.*)$ index.php/$1 [QSA,PT,L]
         </Directory>

        <Proxy *>
                Order deny,allow
                Allow from all
        </Proxy>
        ProxyRequests off
        ProxyPass /api http://api.botue.com/v8
</VirtualHost>
```