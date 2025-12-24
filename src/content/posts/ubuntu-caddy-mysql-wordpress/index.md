---
title: Caddy安装WordPress
description: 'Racknerd VPS Ubuntu20.04配置Caddy、MySQL、PHP安装WordPress.'
published: 2025-01-30
slug: caddy-wordpress
image: "./wordpress-581849_1280.jpg"
category: Code
tags: [Ubuntu, Caddy, PHP, MySQL, WordPress]
---



每次看到别人的博客很赞就又开始嫌弃现在的HUGO，这几天看的几个WordPress的博客都超级好看，以前也用过WordPress，就想着再装个玩玩。
## 安装MySQL数据库
按[在Ubuntu 22.04 LTS 上安装 MySQL两种方式：在线方式和离线方式_ubuntu离线安装mysql-CSDN博客](https://blog.csdn.net/weixin_45626288/article/details/133220238)：
```shell
sudo apt update
sudo apt install -y mysql-server
```
装完启动MySQL，设置开机自启。

```shell
sudo systemctl start mysql
sudo systemctl enable mysql
```
检查MySQL状态：
```shell
sudo systemctl status mysql
```
Ubuntu20.04默认安装的是MySQL8.0，修改默认登录密码，如果是MySQL5.7的可按照[在Ubuntu 22.04 LTS 上安装 MySQL两种方式：在线方式和离线方式_ubuntu离线安装mysql-CSDN博客](https://blog.csdn.net/weixin_45626288/article/details/133220238)修改。


MySQL8.0修改Root用户SQL登录密码：
```sql
# 登录mysql，在默认安装时如果没有让我们设置密码，则直接回车就能登录成功。
sudo mysql -uroot -p
# 设置密码 mysql8.0
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
# 刷新缓存
flush privileges;
```
新建wordpress数据库，新增管理wordpress数据库的用户：
```sql
CREATE DATABASE wordpressdb;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'wordpressuser密码';
GRANT ALL PRIVILEGES ON wordpressdb.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
exit;
```
这里的wordpressdb、wordpressuser及wordpressuser密码修改成自己的，这些要记下，后面要填到WordPress解压包里的wp-config.php里。


## 安装Caddy
按[Install — Caddy Documentation](https://caddyserver.com/docs/install)安装：

```shell
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl
```
```shell
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg 
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
```
```shell
sudo apt update 
sudo apt install caddy
```
安装完成后检查Caddy服务状态：
```shell
sudo systemctl status caddy
```

## 安装PHP
按[在 Ubuntu 22.04 上轻松安装 Caddy 和 PHP 8.1](https://cn.linux-console.net/?p=34595)，先安装PHP，Ubuntu20.04默认安装的是PHP7.4:

```shell
sudo apt install php-fpm php-mysql php-gd php-cli php-curl php-mbstring php-zip php-opcache php-xml php-xmlrpc
```
启动PHP，设置自启，这个我忘了是哪篇文章看来的:
```shell
systemctl start php7.4-fpm
systemctl enable php7.4-fpm
```
## 配置Caddyfile
```shell
sudo vi /etc/caddy/Caddyfile
```
不喜欢用Vi的也可以直接用Ftp管理软件修改Caddyfile文件，把yourdomain改为你绑定的域名，以下设置中网站根目录为/srv/www/caddy，根据自己实际情况填写。
```apache
yourdomain { 
    root * /srv/www/caddy 
    encode gzip zstd 
    php_fastcgi unix//run/php/php7.4-fpm.sock
    file_server
}
```
修改保存后，重启Caddy服务：
```shell
sudo systemctl restart caddy
```
## 安装WordPress
网站根目录所有者改成www-data，我前面没改，WP后台修改site-logo上传不了图片：
```shell
chown -R www-data:www-data /srv/www/caddy
```
压缩包[官网](https://wordpress.org/download/)下载的，[xftp](https://www.xshell.com/zh/xftp/)上传到网站根目录/srv/www/caddy。重命名wp-config-sample.php为wp-config.php,修改wordpress数据库信息：

```php
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpressdb' );

/** Database username */
define( 'DB_USER', 'wordpressuser' );

/** Database password */
define( 'DB_PASSWORD', 'wordpressuser密码' );
```
Authentication unique keys and salts可以在[WordPress.org secret-key service](https://api.wordpress.org/secret-key/1.1/salt/)生成后复制覆盖wp-config.php中以下部分。
```php
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );
```
保存上传。

现在就可以在浏览器输入绑定网址打开WordPress欢迎页面填写站点信息了。


>封面图片由<a href="https://pixabay.com/zh/users/doki7-646987/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=581849">Dok Sev</a>在<a href="https://pixabay.com/zh//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=581849">Pixabay</a>上发布