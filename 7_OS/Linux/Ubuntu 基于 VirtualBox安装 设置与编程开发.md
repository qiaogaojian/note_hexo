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

3.1设置Launcher到底部

按下 ctrl + alt + t 打开终端输入以下命令

```sh
gsettings set com.canonical.Unity.Launcher launcher-position Bottom
```

3.2设置主题

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