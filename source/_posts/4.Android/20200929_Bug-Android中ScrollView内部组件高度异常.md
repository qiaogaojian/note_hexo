# Android中ScrollView内部组件高度异常

## ScrollView内部的组件被压扁

ScrollView里只放一个元素.当ScrollView里的元素想填满ScrollView时，使用”fill_parent”是不管用的，必需为ScrollView设置：android:fillViewport=”true。 当ScrollView没有fillVeewport=“true”时, 里面的元素(比如LinearLayout)会按照wrap_content来计算(不论它是否设了”fill_parent”),而如果LinearLayout的元素设置了fill_parent,那么也是不管用的，因为LinearLayout依赖里面的元素，而里面的元素又依赖LinearLayout,这样自相矛盾.所以里面元素设置了fill_parent，也会当做wrap_content来计算.

## 参考链接

[ScrollView与其他组件的冲突问题](https://blog.csdn.net/ljb568838953/article/details/52563452?locationNum=10)

[在ScrollView下加入的组件，不能自动扩展到屏幕高度](https://www.cnblogs.com/CharlesGrant/p/4727799.html)