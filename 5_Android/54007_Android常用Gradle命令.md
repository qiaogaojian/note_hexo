# Android常用Gradle命令

## 简介

## gradle wrapper

每个基于gradle构建的工程都有一个gradle本地代理，叫做 gradle wrapper
在 /gradle/wrapper/gralde-wrapper.properties 目录中声明了指向目录和版本

官方的各个版本的代理下载地址 https://services.gradle.org/distributions/
如果 gradle 初次构建缓慢，可以手动下载代理放到${USER}/.gradle/wrapper/dists下

本地建立文件 gradle.properties 或者在用户的 .gradle目录下建立 gradle.properties 文件作为全局设置，参数有

```sh
# 开启并行编译
org.gradle.parallel=true
# 开启守护进程
org.gradle.daemon=true
# 按需编译
org.gradle.configureondemand=true
# 设置编译jvm参数
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
# 设置代理
systemProp.http.proxyHost=127.0.0.1
systemProp.http.proxyPort=10384
systemProp.https.proxyHost=127.0.0.1
systemProp.https.proxyPort=10384
# 开启JNI编译支持过时API
android.useDeprecatedNdk=true
```

安装一个全局的gradle，并配置好Path变量，避免每个项目重复下载，这样后面编译项目就可以直接运行gradle build

### 快速构建命令

```sh
# 查看构建版本
./gradlew -v
# 清除build文件夹
./gradlew clean
# 检查依赖并编译打包
./gradlew build
# 编译并安装debug包
./gradlew installDebug
# 编译并打印日志
./gradlew build --info
# 译并输出性能报告，性能报告一般在 构建工程根目录 build/reports/profile
./gradlew build --profile
# 调试模式构建并打印堆栈日志
./gradlew build --info --debug --stacktrace
# 强制更新最新依赖，清除构建并构建
./gradlew clean build --refresh-dependencies
```

注意build命令把 debug、release环境的包都打出来的
如果需要指定构建使用如下命令

```sh
# 编译并打Debug包
./gradlew assembleDebug
# 这个是简写 assembleDebug
./gradlew aD
# 编译并打Release的包
./gradlew assembleRelease
# 这个是简写 assembleRelease
./gradlew aR
```

## 参考链接

[Gradle Android-build 常用命令参数及解释](https://www.jianshu.com/p/a03f4f6ae31d)