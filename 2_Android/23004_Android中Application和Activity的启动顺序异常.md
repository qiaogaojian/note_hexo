# Android中Application和Activity的启动顺序异常

## 发现Activity的OnCreate() 先执行   Application中的OnCreate() 不执行

## 错误分析

没有在 AndroidManifest 文件中指明 Application:name 属性

## 知识扩展

application 创建以后就会调用onCreate()
接下来才去查找对应的Activity并创建,并执行一列表的生命周期
只要知道这两个类的生命周期即可

[Application, Activity, ContentProvider启动顺序](https://blog.csdn.net/beyond702/article/details/49666809)