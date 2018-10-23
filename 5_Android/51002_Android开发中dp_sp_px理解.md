# Android开发中 dp sp px 的理解

## 基础概念

- px ： 其实就是像素单位，比如我们通常说的手机分辨列表800*400都是px的单位

- dp ： 设备独立像素 (device independent pixels) 也叫 dip ，在不同的像素密度的设备上会自动适配

- sp ： 同dp相似，还会根据用户手机系统的字体大小来缩放

### drawable文件夹有

ldpi、mdpi、hdpi、xhdpi四种。

### dpi指像素/英寸

而ldpi指dpi120，

mdpi指dpi160， icon48*48    一般指320*480，是标准的 dp大约是1.5倍的pix

hdpi指dpi240，  icon72*72   480*800

xhdpi指dpi320,   icon96*96   720*1280

xxhdpi指dpi480   icon144*144 1080*1920

![dp1](https://note.youdao.com/yws/public/resource/749aabcc6fd96f41ff3179f1e1012294/xmlnote/7FE5D942C3A344CFB7DDEC99CC1CF47B/3744)

![dp2](https://note.youdao.com/yws/public/resource/749aabcc6fd96f41ff3179f1e1012294/xmlnote/A342AAD9D65B4C1C9A4E0EC9EBB75932/3746)

状态栏高度：50 px

导航栏高度：96 px

标签栏高度：96 px

Android最近出的手机都几乎去掉了实体键，把功能键移到了屏幕中，当然高度也是和标签栏一样的：96 px
内容区域高度为：1038 px （1280-50-96-96=1038）

![sp](https://note.youdao.com/yws/public/resource/749aabcc6fd96f41ff3179f1e1012294/xmlnote/70B39302D43E4B6D818FAF46DFCB4643/4802)

切图720*1280就行了

## 字体

字号采用12sp(small)、14sp(normal)、18sp(large)、22sp(larger),40sp(huge)等四个级别来设计