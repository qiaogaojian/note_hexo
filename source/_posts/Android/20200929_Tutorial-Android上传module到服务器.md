# Android上传module到服务器

<!-- TOC -->

- [Android上传module到服务器](#android上传module到服务器)
    - [配置maven仓库地址 用户名 密码](#配置maven仓库地址-用户名-密码)
    - [配置 gradle.properties](#配置-gradleproperties)
    - [配置 build.gralde](#配置-buildgralde)

<!-- /TOC -->

## 配置maven仓库地址 用户名 密码

项目级build.gradle

``` java
allprojects {
    repositories {
        google()
        jcenter()
        // maven仓库
        maven {
            credentials {
                username 'qiaogaojian'
                password '123456'
            }
            url 'http://47.94.255.178:8081/repository/android'
        }
    }
}
```

## 配置 gradle.properties

在mudule文件夹内新建gradle.properties文件,配置信息如下

``` java
aar_url= http://47.94.255.178:8081/repository/android/
username = qiaogaojian
password = 123456
sdk_version = 1.0.0
artifactId = locale
groupId = com.sdbean.localize
```

## 配置 build.gralde

在module级build.gradle内新建上传任务 gradle执行这个任务即可上传到服务器

``` java
apply plugin: 'maven'
uploadArchives {
    repositories.mavenDeployer {
        repository(url: project.aar_url) {
            authentication(userName: project.username, password: project.password)
        }
        pom.version = project.sdk_version
        pom.artifactId = project.artifactId
        pom.groupId = project.groupId
    }
}
```