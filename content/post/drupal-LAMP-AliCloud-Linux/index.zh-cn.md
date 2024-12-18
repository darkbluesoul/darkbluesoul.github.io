+++
author = "Darkblue Soul"
title = "Alibaba Cloud Linux安装Drupal10.0.7"
date = "2023-04-20 09:57:10 +0800"
description = "Alibaba Cloud Linux 3.2104 LTS 64位部署LAMP环境，安装Drupal10.0.7及一些问题的解决办法。"
categories = [
    "Web"
]
tags = [
    "Drupal", "PHP", "phpMyAdmin", "MySQL", "Apache"
]
image = "drupal.png"
+++
想学下Drupal，按[部署LAMP环境（Alibaba Cloud Linux 3）](https://help.aliyun.com/document_detail/460773.html?spm=a2c4g.440490.0.0.1d2e38d3r4E6ag)部署环境，但是PHP7.4没法安装Drupal10，所以修改了一下。其实还是挺折腾的，弄了一周多的样子，各种踩坑，装好以后也是各种报错。好在这次安装基本都做了笔记，稍微整理下，供参考。

## **Apache2.4**
[Compiling and Installing - Apache HTTP Server Version 2.4](https://httpd.apache.org/docs/2.4/install.html)

```shell
yum install httpd
```

## **Mysql8.0**
```shell
wget http://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm && yum -y install mysql80-community-release-el7-7.noarch.rpm && yum -y install mysql-community-server --nogpgcheck
```

## **PHP8.1.18**
修改了下[部署LAMP环境（Alibaba Cloud Linux 3）](https://help.aliyun.com/document_detail/460773.html?spm=a2c4g.440490.0.0.1d2e38d3r4E6ag)里的安装PHP命令：
```shell
rpm -ivh --nodeps https://rpms.remirepo.net/enterprise/remi-release-8.rpm
```

```shell
dnf update -y dnf libdnf
sed -i 's/PLATFORM_ID="platform:al8"/PLATFORM_ID="platform:el8"/g' /etc/os-release 
yum -y module install php:remi-8.1 
sed -i 's/PLATFORM_ID="platform:el8"/PLATFORM_ID="platform:al8"/g' /etc/os-release
```

## **phpMyAdmin5.2.1**
```shell
mkdir -p /var/www/html/phpmyadmin
```

```shell
cd
wget --no-check-certificate https://files.phpmyadmin.net/phpMyAdmin/5.2.1/phpMyAdmin-5.2.1-all-languages.zip
```
```shell
unzip phpMyAdmin-5.2.1-all-languages.zip
```
```shell
mv phpMyAdmin-5.2.1-all-languages/*  /var/www/html/phpmyadmin
```
部署成功后，phpMyAdmin无法登录显示“缺少mysqli扩展”、修复后登录数据库，页面底部如果显示“配置文件现在需要一个短语密码”、“缺少mcrypt扩展”，解决方法如下：

### 缺少mysqli扩展：

安装mysqli（参考[linux下php-mysql拓展安装](https://www.cnblogs.com/caryfang/p/5439299.html)）：

```shell
yum install php-mysql
```

### 缺少mcrypt扩展：
安装mcrypt（参考[phpMyAdmin“缺少 mcrypt 扩展。请检查 PHP 配置。”解决办法](https://www.osyunwei.com/archives/2053.html)）：

```shell
yum  install  php-mcrypt  libmcrypt  libmcrypt-devel
```


### 配置文件现在需要一个短语密码：
按百度经验[配置文件现在需要一个短语密码](https://jingyan.baidu.com/article/adc815137733faf722bf7358.html) 修改。

## **Drupal10.0.7**

修改了[手动搭建Drupal网站](https://help.aliyun.com/document_detail/162768.html?spm=a2c4g.162767.0.0.17f51a18NaWePM)这篇帮助里的一些命令，换了版本号。

下载Drupal：
```shell
cd
wget https://ftp.drupal.org/files/projects/drupal-10.0.7.zip
```
解压Drupal：
```shell
yum install unzip -y
unzip drupal-10.0.7.zip
```
将安装文件移动到Apache的网站根目录:
```shell
mv drupal-10.0.7/* /var/www/html
mv drupal-10.0.7/.htaccess /var/www/html
mv drupal-10.0.7/.csslintrc drupal-10.0.7/.editorconfig drupal-10.0.7/.eslintignore drupal-10.0.7/.eslintrc.json drupal-10.0.7/.gitattributes /var/www/html
```
下载中文翻译包：
```shell
cd /var/www/html
wget -P sites/default/files/translations https://ftp.drupal.org/files/translations/all/drupal/drupal-10.0.7.zh-hans.po
```
后续步骤同[手动搭建Drupal网站](https://help.aliyun.com/document_detail/162768.html?spm=a2c4g.162767.0.0.17f51a18NaWePM)。

### 安装过程中CleanURL未激活

/etc/httpd/conf/httpd.conf，找到<Directory "/var/www/html">，将其中的
```apache
AllowOverride None
```
修改为

```apache
AllowOverride All
```
### php-gd库缺失：
```shell
yum -y install php-gd
```
### Critical security announcements that starts with "Failed to fetch security advisory data"

参考[Responding to critical security update advisories | Updating Drupal | Drupal Wiki guide on Drupal.org](https://www.drupal.org/docs/updating-drupal/responding-to-critical-security-update-advisories#s-troubleshooting-the-advisory-feed)
按[PHP OpenSSL requirements | System requirements | Drupal Wiki guide on Drupal.org](https://www.drupal.org/docs/system-requirements/php-openssl-requirements)
修改了`settings.php` file:

```php
$settings['update_fetch_with_http_fallback'] = TRUE;
```
