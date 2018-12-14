# DosBox启动设置

## 位置

c:/用户/用户名/appdata/local/dosbox/dosbox-0.74.conf

## 指令

配置文件末尾添加以下指令:

```sh
mount c: d:\asm
set PATH=%PATH%;c:\;
c:
```