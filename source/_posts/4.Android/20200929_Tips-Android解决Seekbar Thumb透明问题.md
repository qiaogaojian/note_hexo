# 解决Seekbar Thumb透明问题

## 问题描述

Thumb不是矩形时,透明部分会和背景冲突

![](/images/2020-09-29-16-19-15.png)

第一个是修改后的 后两个是有问题的

## 解决办法

布局中Seekbar组件添加  android:splitTrack="false"

![](/images/2020-09-29-16-12-36.png)