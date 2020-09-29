# Android开发中常用adb命令的使用

<!-- TOC -->

- [Android开发中常用adb命令的使用](#android开发中常用adb命令的使用)
    - [安装软件](#安装软件)
    - [卸载软件](#卸载软件)
    - [无线连接](#无线连接)
    - [错误解决](#错误解决)

<!-- /TOC -->

使用adb命令前首先确保手机通过数据线和电脑连接,手机usb调试已打开,然后弹出验证框时点同意来给电脑权限.

## 安装软件

- 命令格式

    adb install 安装包完整路径

- 示例

    ``` adb
    adb install d:\software\test.apk
    ```

## 卸载软件

- 命令格式

    adb uninstall 软件包名

- 示例

    ``` adb
    adb uninstall com.sdbean.test
    ```

- 获取apk包名

    ``` adb
    aapt dump badging d:\software\test.apk
    ```

    aapt程序路径: AndroidSdk\build-tools\28.0.3

- 获取当前应用包名

    ``` adb
    adb shell dumpsys window w |findstr \/ |findstr name=
    ```

## 无线连接

- 连接数据线输入命令打开手机远程访问端口

    ``` adb
    adb tcpip 5555
    ```

- 查看手机内网ip地址

    ``` adb
    adb shell ifconfig wlan0
    ```

- 无线连接手机

    ``` adb
    adb connect 192.168.1.66:5555
    ```

    然后就可以进行无线安装 调试 投屏等操作了

## 错误解决

device offline 和 server version doesn't match this client 等常见错误的解决方法如下

``` adb
adb kill-server

adb start-server

adb remount
```
