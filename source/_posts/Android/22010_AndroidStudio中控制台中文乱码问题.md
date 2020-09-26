# 解决控制台中文乱码问题

## 在项目gradle.build文件中添加以下代码即可

``` gradle
tasks.withType(JavaCompile) {
    options.encoding = "UTF-8"
}
```

## 完整如下

```gradle
apply plugin: 'java-library'

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
}
tasks.withType(JavaCompile) {
    options.encoding = "UTF-8"
}
sourceCompatibility = "6"
targetCompatibility = "6"
```