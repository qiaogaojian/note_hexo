# trying_to_draw_too_large_ bitmap

## 问题描述

加载一个大背景图片时,报错 trying to draw too large(138078000bytes) bitmap

## 最终解决方案

```java
Glide.with(getApplicContext())
        .load(R.drawable.cge_gamehall_bg)
        .diskCacheStrategy(DiskCacheStrategy.ALL)
        .into(new SimpleTarget<GlideDrawable>()
        {
            @Override
            public void onResourceReady(GlideDrawable resource, GlideAnimation<? super GlideDrawable> glideAnimation)
            {
                gamehallBinding.gameHallBg.setImageDrawable(resource);
            }
        });
```

## 其他说法(未验证)

这里就不翻译了，意思就是说你将高分辨率图片放在了低分辨率文件夹下。
例如：图片的分辨率是属于xxhdpi的，而你将这张图片放在了drawable-xhdpi或者比这个还低的文件夹下，就会报这个错，解决的办法：
1.人为的将这张图片的分辨率降低(一般不这样做)
2.将高分辨率的图片放在drawable-xxhdpi或者drawable-xxxhdpi下即可

当然，之所以会出现这些问题都是UI切图不注意大小或者工程师放置图片位置不规范导致的，如果严格的按照andorid开发规范的要求来做的话，是根本不会出现这种问题的。

采用第二种方式的话，在调试安装apk的时候是没有问题的，但是在打包安装的时候会报软件包安装程序已停止的错误，原因是drawable-xhdpi文件夹下没有图片，将图片分辨率降低放入drawable-xhdpi文件夹下再次打包安装就没有问题了。