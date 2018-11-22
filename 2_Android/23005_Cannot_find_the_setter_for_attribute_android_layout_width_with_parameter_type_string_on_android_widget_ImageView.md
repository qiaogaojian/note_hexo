# Cannot find the setter for attribute 'android:layout_width' with parameter type int on android.widget.ImageView

## 解决思路

最早我以为是xml文件哪里写错了,排查了一遍没有发现错误,然后删除.gradle文件夹和.idea文件夹重新打开,问题解决.
估计是因为之前改了项目名字造成的错误.