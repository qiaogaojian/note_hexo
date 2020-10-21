# Android 开发中常用 adb 命令的使用

<!-- TOC -->

- [Android 开发中常用 adb 命令的使用](#android-开发中常用-adb-命令的使用)
    - [安装软件](#安装软件)
    - [卸载软件](#卸载软件)
    - [无线连接](#无线连接)
    - [抓取 log](#抓取-log)
    - [当前连接设备](#当前连接设备)
    - [查看所有进程内存信息](#查看所有进程内存信息)
    - [查看特定进程内存信息](#查看特定进程内存信息)
    - [获取崩溃日志](#获取崩溃日志)
    - [错误解决](#错误解决)

<!-- /TOC -->

使用 adb 命令前首先确保手机通过数据线和电脑连接,手机 usb 调试已打开,然后弹出验证框时点同意来给电脑权限.

## 安装软件

- 命令格式

  adb install 安装包完整路径

- 示例

  ```adb
  adb install d:\software\test.apk
  ```

## 卸载软件

- 命令格式

  adb uninstall 软件包名

- 示例

  ```adb
  adb uninstall com.sdbean.test
  ```

- 获取 apk 包名

  ```adb
  aapt dump badging d:\software\test.apk
  ```

  aapt 程序路径: AndroidSdk\build-tools\28.0.3

- 获取当前应用包名

  ```adb
  adb shell dumpsys window w |findstr \/ |findstr name=
  ```

## 无线连接

- 连接数据线输入命令打开手机远程访问端口

  ```adb
  adb tcpip 5555
  ```

- 查看手机内网 ip 地址

  ```adb
  adb shell ifconfig wlan0
  ```

- 无线连接手机

  ```adb
  adb connect 192.168.1.66:5555
  ```

  然后就可以进行无线安装 调试 投屏等操作了

- 与远程设备断开连接

  ```adb
  adb disconnect 192.168.1.23
  ```

## 抓取 log

1. 输入抓取命令

   ```adb
   adb logcat -v time > xxxx.log
   ```

2. 启动应用并进行相关操作, 直到出现所遇到的问题

3. ctrl + c 结束 log 抓取

## 当前连接设备

```adb
adb devices
```

## 查看所有进程内存信息

```adb
adb shell dumpsys meminfo
```

## 查看特定进程内存信息

```adb
adb shell dumpsys meminfo pid/packagename
```

## 获取崩溃日志

如果您连接了多台设备，则必须使用 -s 选项指定设备。adb -s 8XV7N15C31003476

```adb
adb bugreport D:\path
```

## 错误解决

device offline 和 server version doesn't match this client 等常见错误的解决方法如下

```adb
adb kill-server

adb start-server

adb remount
```
