# AndroidStudioGradle离线安装

## 官网下载对应版本Gradle

## 修改GradleWrapper

修改项目目录"\gradle\wrapper"下的，gradle-wrapper.properties文件，中的distributionUrl为

```url
https\://services.gradle.org/distributions/gradle-3.3-all.zip
```

## 安装位置

将该gradle-3.3-all.zip文件复制到一下位置C:\Users\你的用户名\.gradle\wrapper\dists\gradle-3.3-all\随机字符串\下，重新打开android studio即可