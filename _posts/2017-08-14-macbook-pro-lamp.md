---
layout: post_layout
title: macbook pro 配置 lamp 服务器解决办法
time: 2017年08月15日 星期一
location: 上海
published: true
excerpt_separator: '```'
---

## macOS 配置 apache 和 php

- 终端输入

  ```bash
  sudo vim /etc/apache2/httpd.conf
  ```

  \#LoadModule php5_module libexec/apache2/libphp5.so // 关联 php5

- 重启 apache

  ```bash
  sudo apachectl restart
  ```

- 访问 apache 默认页面，如果出现表示配置成功

  浏览器地址栏输入：http://localhost/

## 配置 Mysql

- 下载 mysql 的 dmg 安装包，下载地址https://dev.mysql.com/downloads/mysql/

- 默认用户名：root，安装会随机生成密码 newpass 的弹出框，复制密码以备后用，也可在右上角通知处再次查看

- 登录 mysql 数据库

  ```bash
  mysql -uroot -p
  ```

  ```bash
  newpass	//输入随机生成的密码
  ```

- 修改 root 密码:root

  ```bash
  mysql -uroot password root
  ```

## 配置 phpMyAdmin 管理 mysql 数据库

- 下载 phpMyAdmin，下载地址：<http://www.phpmyadmin.net/home_page/downloads.php>

- 解压到/Library/WebServer/Documents/，重命名文件夹名为 phpMyAdmin

- 在 phpMyAdmin 目录命令行输入

  ```bash
  cp config.sample.inc.php config.inc.php
  vim config.inc.php
  ```

  修改 phpMyAdmin 配置文件与 mysql 数据库关联起来

  ```bash
  $cfg['blowfish_secret'] = '';//用于Cookie加密，随意的长字符串
  $cfg['Servers'][$i]['host'] = '127.0.0.1';//MySQL 守护程序做了IP绑定
  ```

现在可以在浏览器中输入 URL：<http://localhost/phpmyadmin/>
通过 mysql 的 dmg 包安装会在偏好设置中配置 mysql，可以快捷启动或是关闭
