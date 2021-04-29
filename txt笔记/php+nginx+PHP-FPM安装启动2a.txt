05.04 06:30
php+nginx+PHP-FPM安装启动2
php+nginx+PHP-FPM安装启动
termux封装的php版本是php 7.2.5

安装PHP
pkg install php
查看下版本


自PHP5.4之后 PHP内置了一个Web 服务器,来在termux下尝试下PHP Web Server的简单使.

编写测试文件
在家目录下建一个www文件夹:mkdir www
在www文件夹下新建一个index.php文件,其内容为

<?php phpinfo();?>
 
启动WebServer
php -S 127.0.0.1:8080 -t www/
浏览器访问效果如下:


nginx
Nginx 是一个高性能的 Web 和反向代理服务器, 它具有有很多非常优越的特性.

安装nginx包
pkg install nginx
切换root用户
尝试下能不能解析默认的index.html主页
这个文件在termux上的默认位置为/data/data/com.termux/files/usr/share/nginx/html/index.html
切换root用户

默认的普通权限无法启动nginx,需要模拟root权限才可以

没有这个命令的话,手动安装pkg install proot包

termux-chroot
进入模拟的root环境

启动nginx
在模拟的root环境下启动nginx

nginx
termux上nginx默认的端口是8080
查看下8080端口是否在运行

netstat -an |grep 8080
 
然后手机本地直接访问:http://127.0.0.1:8080 查看下nginx是否正常启动.
效果图


这样一个默认的nginx服务就起来了,但是意义不大,得配置一下可以解析php才会有更大的意义.


停止nginx服务
直接杀掉占用端口的进程,具体端口以实际情况为准.

fuser -k 8080/tcp
重启nginx服务

nginx -s reload

nginx解析PHP

nginx解析PHP这里单独拿出一级标题来叙述,成功解析的话,下面安装wordpress等cms就会轻松很多.
nginx本身不能处理PHP，它只是个web服务器，当接收到php请求后发给php解释器处理,nginx一般是把请求发fastcgi管理进程处理,PHP-FPM是一个

—— ——————

启动服务

启动 php-fpm

php-fpm
启动 nginx

nginx
访问 info.php

打开浏览器，输入 127.0.0.1:8080/info.php ，出现如下页面：


最关键的地方是，直接杀掉占用端口的进程,，

然后重启重启nginx服务重启PHP-FPM

长按  屏幕   More   

选择 Kll  process   关闭多于进程
Kill process: 杀掉当前终端会话进程

Welcome to Termux!

Community forum: https://termux.com/community
Gitter chat:     https://gitter.im/termux/termux
IRC channel:     #termux on freenode

Working with packages:

 * Search packages:   pkg search <query>
 * Install a package: pkg install <package>
 * Upgrade packages:  pkg upgrade

Subscribing to additional repositories:

 * Root:     pkg install root-repo
 * Unstable: pkg install unstable-repo
 * X11:      pkg install x11-repo

Report issues at https://termux.com/issues

The Google Play version of the Termux app no longer
receives updates. For more information, visit:
https://wiki.termux.com/wiki/Termux_Google_Play

~ $ fuser -k 8080/tcp
~ $ nginx -s reload
nginx: [error] open() "/data/data/com.termux/files/usr/tmp/nginx.pid" failed (2: No such file or directory)
~ $ netstat -an |grep 8080
tcp        0      0 192.168.31.212:43388    120.241.21.112:8080     ESTABLISHED
tcp6      32      0 192.168.31.212:48080    183.194.218.72:443      CLOSE_WAIT
~ $ nginx
~ $ nginx -s reload
~ $
~ $ PHP-FPM
PHP-FPM: command not found
~ $
~ $ php-fpm
[04-May-2021 00:06:26] NOTICE: [pool www] 'user' directive is ignored when FPM is not running as root
[04-May-2021 00:06:26] NOTICE: [pool www] 'group' directive is ignored when FPM is not running as root
~ $ nginx
nginx: [emerg] bind() to 0.0.0.0:8080 failed (98: Unknown error)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (98: Unknown error)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (98: Unknown error)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (98: Unknown error)
nginx: [emerg] bind() to 0.0.0.0:8080 failed (98: Unknown error)
nginx: [emerg] still could not bind()
~ $ php-fpm
[04-May-2021 06:10:33] NOTICE: [pool www] 'user' directive is ignored when FPM is not running as root
[04-May-2021 06:10:33] NOTICE: [pool www] 'group' directive is ignored when FPM is not running as root
[04-May-2021 06:10:33] ERROR: unable to bind listening socket for address '127.0.0.1:9000': Address already in use (98)
[04-May-2021 06:10:33] ERROR: FPM initialization failed
~ $


启动 php-fpm

php-fpm
启动 nginx

nginx

PHP-FPM启动成功后就能访问了

打开浏览器，输入 127.0.0.1:8080/
就能访问了

————————
