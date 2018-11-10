# PHP包管理工具Composer的使用

## 安装

### 下载

[链接](https://getcomposer.org/download/)

### 安装

- 把下载的 composer.phar 文件, 移动到php的有php.exe的文件目录下

- 新建 composer.bat 文件 内容如下

```bat
@ECHO OFF
php "%~dp0composer.phar" %*
```

## 配置环境变量

把php目录加入系统环境变量

## 测试

打开cmd 输入composer -v 如果出现composer字符图案则说明安装成功

## 生成json

项目根目录下输入以下命令

```sh
composer init
```

如果没有tls 输入以下命令

```sh
composer config -g -- disable-tls true
```