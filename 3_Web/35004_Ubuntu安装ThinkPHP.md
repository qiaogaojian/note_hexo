# Ubuntu安装ThinkPHP

## 安装Composer

```sh
sudo apt-get install composer
```

## 切换Composer源

```sh
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

## 安装thinkphp5

首先切换到网站根目录 var/www/html

```sh
sudo apt-get install zip unzip php7.0-zip
```

```sh
composer create-project topthink/think thinkphp5  --prefer-dist
```

## 添加Git版本控制

```sh
git config --global user.name "Michael"
git config --global user.email "qiaogaojian@vip.qq.com"
git config --global push.default simple

git init
git add .
git commit -m "init project"

git remote add origin https://gitee.com.qiaogaojian/example.git

git branch --set-upstream-to=origin/master
git pull

git push --set-upstream origin master
git push
```

## 参考链接

[composer方式安装thinkphp5](https://my.oschina.net/inuxor/blog/750717)

[使用git将本地项目推送到码云私有仓库](https://blog.csdn.net/qq_33876553/article/details/80111946)

[Git 2.0 更改 push default 为‘simple’](https://www.oschina.net/news/45585/git-2-x-change-push-default-to-simple)