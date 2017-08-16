---
layout: post_layout
title: macbook pro配置lamp服务器解决办法
time: 2017年08月15日 星期一
location: 上海
pulished: true
excerpt_separator: "```"
---

###  mac os上自带了apache和php，无需另外下载，简单配置如下

+ 终端输入

  ```
  sudo vim /etc/apache2/httpd.conf
  ```

  \#LoadModule php5_module libexec/apache2/libphp5.so

  去掉#解开注释，让apache2关联php5

+ 重启apache

  ```
  sudo apachectl restart
  ```

+ 访问apache默认页面，如果出现表示配置成功

  浏览器地址栏输入：http://localhost/

### 配置Mysql

+ 下载mysql的dmg安装包，下载地址https://dev.mysql.com/downloads/mysql/

+ 默认用户名：root，安装会随机生成密码newpass的弹出框，复制密码以备后用，也可在右上角通知处再次查看

+ 登录mysql数据库

  ```
  mysql -uroot -p
  ```

  ```
  newpass	//输入随机生成的密码
  ```

+ 修改root密码:root

  ```
  mysql -uroot password root
  ```

### 配置phpMyAdmin管理mysql数据库

  + 下载phpMyAdmin，下载地址：http://www.phpmyadmin.net/home_page/downloads.php

  + 解压到/Library/WebServer/Documents/，重命名文件夹名为phpMyAdmin

  + 在phpMyAdmin目录命令行输入

    ```
    cp config.sample.inc.php config.inc.php  
    vim config.inc.php 
    ```

    修改phpMyAdmin配置文件与mysql数据库关联起来

    ```
    $cfg['blowfish_secret'] = '';//用于Cookie加密，随意的长字符串  
    $cfg['Servers'][$i]['host'] = '127.0.0.1';//MySQL守护程序做了IP绑定  
    ```

现在可以在浏览器中输入URL：http://localhost/phpmyadmin/

通过mysql的dmg包安装会在偏好设置中配置mysql，可以快捷启动或是关闭