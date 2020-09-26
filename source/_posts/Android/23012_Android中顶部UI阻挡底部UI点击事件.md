# Android中顶部UI阻隔底部UI点击事件

## 错误分析

今天接到一个bug: 在播放一个全屏webp动画时,竟然可以点击下面被遮挡的按钮并触发点击事件,最终通过设置xml文件属性解决, webp 组件为 Fresco 的 SimpleDraweeView

```xml
android:focusable="true"
android:clickable="true"
```

## 参考引用

[Android: How to prevent any touch events from being passed from a view to the one underneath it?](https://stackoverflow.com/questions/8433387/android-how-to-prevent-any-touch-events-from-being-passed-from-a-view-to-the-on)