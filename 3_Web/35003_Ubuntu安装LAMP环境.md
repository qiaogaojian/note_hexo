# Ubuntu安装LAMP环境

## 更新

```sh
sudo apt-get update
```

## 安装git

```sh
sudo apt-get install git
```

## 安装Apache

```sh
sudo apt-get install apache2
```

### 添加阿里云安全组外网80端口访问

![image.png](https://upload-images.jianshu.io/upload_images/3947109-fc72e93e1a263173.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 测试

浏览器输入IP,如果出现It works代表安装成功

### 查看状态

```sh
// 服务状态
service apache2 status
// 启动服务
service apache2 start
// 停止服务
service apache2 stop
// 重启服务
service apache2 restart
```

### Web目录

```sh
/var/www
```

### 安装目录

```sh
/etc/apache2
```

### 全局配置

```sh
/etc/apache2/apache2.conf
```

### 监听端口

```sh
etc/apache2/ports.conf
```

### 虚拟主机

```sh
/etc/apache2/sites-enabled/000-default.conf
```

## 安装MySql

```sh
sudo apt-get install mysql-server mysql-client
```

### 测试

```sh
mysql -u root -p
showdatabases;
```

### 查看状态

```sh
service mysql status/start/stop/restart
```

### 查看监听端口的情况

```sh
netstat -tunpl 或 netstat -tap
```

## 安装PHP

```sh
sudo apt-get install php
```

测试

```sh
php -v
```

## 安装其他模块

```sh
sudo apt-get install libapache2-mod-php7.0
sudo apt-get install php7.0-mysql
```

### 重启服务

```sh
service apache2 restart
service mysql restart
```

测试Apache能否解析PHP

```sh
nano /var/www/html/phpinfo.php
文件中写：<?php echo phpinfo();?>
浏览器访问：http://ubuntu地址/phpinfo.php，出现PHP Version网页
```

## 修改权限

```sh
sudo chmod 777 /var/www
```

## 安装phpMyAdmin

```sh
sudo apt-get install phpmyadmin
```

服务器选择apache2

配置数据库密码

创建phpMyAdmin快捷方式

```sh
sudo ln -s /usr/share/phpmyadmin /var/www/html
```

## 启动 Apache rewrite模块

```sh
sudo a2enmod rewrite
```

### 重启服务

```sh
service php7.0-fpm restart
service apache2 restart
```

### 测试

```sh
// 访问
http://serverurl/phpmyadmin
```

## 配置Apache

```sh
nano /etc/apache2/apache2.conf
```

添加配置

```sh
AddType application/x-httpd-php .php .htm .html
AddDefaultCharset UTF-8
```

重启Apache服务

```sh
service apache2 restart
```