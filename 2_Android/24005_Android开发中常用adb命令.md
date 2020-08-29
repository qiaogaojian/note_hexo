# Android开发种常用adb命令总结

<!-- TOC -->

- [Android开发种常用adb命令总结](#android开发种常用adb命令总结)
    - [adb devices](#adb-devices)
    - [adb install 安装包](#adb-install-安装包)
    - [adb uninstall 包名](#adb-uninstall-包名)
    - [adb tcpip 5555](#adb-tcpip-5555)
    - [adb connect 192.168.1.23:5555](#adb-connect-1921681235555)
    - [adb disconnect 192.168.1.23](#adb-disconnect-192168123)
    - [adb shell dumpsys meminfo](#adb-shell-dumpsys-meminfo)
    - [adb shell dumpsys meminfo pid/packagename](#adb-shell-dumpsys-meminfo-pidpackagename)
    - [adb logcat -v time > log.txt](#adb-logcat--v-time--logtxt)
    - [adb bugreport D:\path](#adb-bugreport-d\path)

<!-- /TOC -->

## adb devices

当前连接设备

## adb install 安装包

安装软件

## adb uninstall 包名

卸载软件

## adb tcpip 5555

开放远程接口5555

## adb connect 192.168.1.23:5555

远程连接设备

## adb disconnect 192.168.1.23

与远程设备断开连接

## adb shell dumpsys meminfo

查看所有进程内存信息

## adb shell dumpsys meminfo pid/packagename

查看特定进程内存信息

## adb logcat -v time > log.txt

抓取log (ctrl + c 结束抓取)

## adb bugreport D:\path

获取崩溃日志,如果您连接了多台设备，则必须使用 -s 选项指定设备。adb -s 8XV7N15C31003476 bugreport