# Ubuntu VirtualBox安装与设置

## 一.系统安装

- 下载virtualBox 和 ubuntu-16.04.6-desktop-amd64.iso镜像

- 完成安装后打开virtualBox新建虚拟机 如果没有64位选项 重启电脑进入bios开启cpu虚拟化选项(Virtualization)

- 内存设置2G 硬盘设置30G 动态分配大小 硬盘位置选一个空间大的硬盘

- 启动虚拟机 光驱选择前面下载好的镜像进入安装程序

- 语言选择英语

- 安装完成后重启

## 二.基本设置

### 1.更新源

#### 1.1方法一

修改 /etc/apt/sources.list

```sh
#添加阿里源
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

然后更新

```sh
sudo apt-get update
sudo apt-get upgrade
```

#### 1.2方法二

打开软件更新中心选择国内的源 如aliyun

### 2.设置共享文件

打开虚拟机设备菜单 选择安装增强功能

安装完成后在宿主机本地新建共享文件夹hdshare 然后打开虚拟机设置选择共享文件夹选项 固定分配添加刚才新建的文件夹

回到虚拟机 进入/mnt/文件夹新建文件夹hdshare

执行装载命令即可完成共享

```sh
mount -t vboxsf hdshare /mnt/hdshare/
```

### 3.设置Ubuntu外观

#### 3.1设置Launcher到底部

按下 ctrl + alt + t 打开终端输入以下命令

```sh
gsettings set com.canonical.Unity.Launcher launcher-position Bottom
```

#### 3.2设置主题

- 更新

```sh
sudo apt-get update
sudo apt-get upgrade
```

安装unity-tweak-tool

```sh
sudo apt-get install unity-tweak-tool
```

安装 Flatabulous 主题

```sh
sudo add-apt-repository ppa:noobslab/themes
sudo apt-get update
sudo apt-get install flatabulous-theme
```

安装Flatabulous 主题的配套图标

```sh
sudo add-apt-repository ppa:noobslab/icons
sudo apt-get update
sudo apt-get install ultra-flat-icons
```

按Windows键，输入unity，打开unity-tweak-tool 打开主题设置选择flatabulous  打开icon设置选择ultraflat

## 三.编程开发

### 1.以太坊Dapp开发环境

#### 1.1 安装Node.js

进入/home/username/downloads目录下载安装包

```sh
sudo wget https://nodejs.org/dist/v8.10.0/node-v8.10.0-linux-x64.tar.gz
```

解压

```sh
tar zxvf node-v8.10.0-linux-x64.tar.gz
```

/home目录下新建dev文件夹 移动解压后的node到dev目录

```sh
mv node-v8.10.0-linux-x64 ../../dev/node8
```

设置node环境变量

```sh
echo "export NODE_HOME=/home/dev/node8" >> .bashrc
echo "export NODE_PATH=$NODE_HOME/lib/node_modules" >> .bashrc
echo "export PATH=$NODE_HOME/bin:$PATH" >> .bashrc
```

设置永久环境变量

> /etc/profile和/etc/profile.d都是常用的设置环境的地方。其中/etc/profile.d文件夹来源于/etc/profile，在该目录下的*.sh，即以sh为后缀的文件都会被加载。
类似地，不推荐使用/etc/bash.bashrc，因为在图形界面环境下启动程序时，不会加载它里边的环境变量设置。

```sh
gedit /etc/profile
```

在打开的文件后面添加下面代码

```sh
export NODE_HOME=/home/dev/node8
export NODE_PATH=$NODE_HOME/lib/node_modules
export PATH=$NODE_HOME/bin:$PATH
```

更新并查看环境变量

```sh
source /etc/profile
export
```

验证：

```sh
~$ node –v
v8.10.0
```

#### 1.2 安装节点仿真器

```sh
npm install –g ganache-cli
```

验证：

```sh
~$ ganache-cli --version
Ganache CLI v6.0.3 (ganache-core: 2.0.2)
```

#### 1.3 安装solidity编译器

```sh
npm install –g solc
# 如果需要安装特定版本solc 需要在项目目录下安装 然后再设置vscode solidity插件的编译器版本
npm install solc@0.4.18
```

如果提示没有权限,修改相应文件夹的权限

```sh
chmod 777 * -R
```

验证：

```sh
~$ solcjs -–version
0.40.2+commit.3155dd80.Emscripten.clang
```

#### 1.4 安装web3

```sh
# 如果没有git 要先安装git
sudo apt-get install git
npm install –g web3@0.20.2
```

验证：

```sh
~$ node –p 'require("web3")'
{[Function: Web3]
  providers:{…}}
```

#### 1.5 安装truffle框架

```sh
npm install –g truffle
```

验证：

```sh
~$ truffle version
Truffle v4.1.3 (core 4.1.3)
```

#### 1.6 安装webpack

```sh
npm install –g webpack@3.11.0
```

验证：

```sh
~$ webpack –v
3.11.0
```

#### 新建Dapp项目
