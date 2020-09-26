# Git常用命令速查表

<!-- TOC -->

- [Git常用命令速查表](#git常用命令速查表)
    - [常用命令](#常用命令)
        - [1.下载项目](#1下载项目)
        - [2.添加更改](#2添加更改)
        - [3.提交文件](#3提交文件)
        - [4.上传代码](#4上传代码)
        - [5.下载代码](#5下载代码)
        - [6.显示所有分支](#6显示所有分支)
        - [7.切换到分支](#7切换到分支)
        - [8.新建本地分支](#8新建本地分支)
        - [9.推送本地分支到远程](#9推送本地分支到远程)
        - [10.删除文件](#10删除文件)
        - [11.撤销本地所有未提交文件的修改](#11撤销本地所有未提交文件的修改)
        - [12.查看提交历史](#12查看提交历史)
        - [13.查看git config](#13查看git-config)
        - [14.查看远程仓库](#14查看远程仓库)
        - [15.删除远程仓库](#15删除远程仓库)
        - [16.Gitlab新建仓库命令](#16gitlab新建仓库命令)
    - [Git命令表](#git命令表)

<!-- /TOC -->

## 常用命令

### 1.下载项目

```sh
git clone https://gitee.com/qiaogaojian/MyGames.git
```

### 2.添加更改

```sh
git add .
```

### 3.提交文件

```sh
git commit -m "commit message"
```

退出vi编辑器: esc + : + wq/q

### 4.上传代码

```sh
git push
```

### 5.下载代码

```sh
git pull
```

### 6.显示所有分支

```sh
git branch
```

### 7.切换到分支

```sh
git checkout master
```

### 8.新建本地分支

```sh
git checkout -b TestBranch
```

### 9.推送本地分支到远程

```sh
git push origin TestBranch
git push --set-upstream origin TheScrollofTaiwu
```

### 10.删除文件

```sh
git rm <file>
```

### 11.撤销本地所有未提交文件的修改

```sh
git checkout . //撤销文件更改
git clean -xdf //清除未跟踪文件
```

### 12.查看提交历史

```sh
git log
```

退出log: 英文状态下按q

### 13.查看git config

```sh
git config --global user.name
git config --global user.email
```

### 14.查看远程仓库

``` sh
git remote -v
```

### 15.删除远程仓库

``` sh
git remote rm origin
```

### 16.Gitlab新建仓库命令

1. Git global setup

``` sh
git config --global user.name "qiaogaojian"
git config --global user.email "qiaogaojian@vip.qq.com"
```

2. Create a new repository

``` sh
git clone git@gitlab.53site.com:qiaogaojian/testremote.git
cd testremote
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

3. Existing folder

``` sh
cd existing_folder
git init
git remote add origin git@gitlab.53site.com:qiaogaojian/testremote.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

4. Existing Git repository

``` sh
cd existing_repo
git remote rename origin old-origin
git remote add origin git@gitlab.53site.com:qiaogaojian/testremote.git
git push -u origin --all
git push -u origin --tags
```

## Git命令表

![image.png](https://upload-images.jianshu.io/upload_images/3947109-efdd076117d53040.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
