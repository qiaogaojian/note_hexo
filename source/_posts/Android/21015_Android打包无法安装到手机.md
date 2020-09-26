# Android打包无法安装到手机

## 错误记录

![](img/2019-08-28-15-58-04.png)

以上为安装时提示的异常情况，解决办法：
在 gradle.properties 文件中添加 android.injected.testOnly=false 即可解决问题