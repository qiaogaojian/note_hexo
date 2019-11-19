# Android安装包反编译教程

<!-- TOC -->

- [Android安装包反编译教程](#android安装包反编译教程)
    - [工具下载](#工具下载)
    - [用apktool反编译apk得到图片、XML配置、语言资源等文件](#用apktool反编译apk得到图片xml配置语言资源等文件)
    - [使用dex2jar反编译apk得到Java源代码](#使用dex2jar反编译apk得到java源代码)
    - [使用jd-gui打开classes-dex2jar.jar就可以看到源代码了](#使用jd-gui打开classes-dex2jarjar就可以看到源代码了)

<!-- /TOC -->

## 工具下载

- [apktool ——(资源文件获取，可以提取出图片文件和布局文件进行使用查看)](https://bitbucket.org/iBotPeaches/apktool/downloads/)

- [dex2jar——(将apk反编译成java源码(classes.dex转化成jar文件))](https://sourceforge.net/projects/dex2jar/files/)

- [jd-gui——(查看APK中classes.dex转化成出的jar文件，即源码文件)](http://java-decompiler.github.io/)

## 用apktool反编译apk得到图片、XML配置、语言资源等文件

1、打开命令行工具(cmd) & cd到工具所在目录

``` sh
cd D:\逆向\tool
```

2、用apktool反编译得到图片等

注意：“[]”内为你的具体值。

``` sh
java -jar [apktool_2.3.4.jar] d -f [apk地址] -o [输出的目录名称]
//---------------------正确结果------------------
I: Using Apktool 2.3.4 on com.yc.flagfit2_1.2.9_29.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
S: WARNING: Could not write to (C:\Users\Administrator\AppData\Local\apktool\fra
mework), using C:\Users\ADMINI~1\AppData\Local\Temp\ instead...
S: Please be aware this is a volatile directory and frameworks could go missing,
 please utilize --frame-path if the default storage directory is unavailable
I: Loading resource table from file: C:\Users\ADMINI~1\AppData\Local\Temp\1.apk
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Baksmaling classes.dex...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...
```

## 使用dex2jar反编译apk得到Java源代码

1、解压apk得到classes.dex。

2、将classes.dex放到dex2jar-2.0文件夹内。

3、cmd输入命令：

``` sh
d2j-dex2jar.bat classes.dex
```

得到classes-dex2jar.jar

## 使用jd-gui打开classes-dex2jar.jar就可以看到源代码了