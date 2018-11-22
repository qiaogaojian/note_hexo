# Android内存泄漏问题

## 什么是内存泄漏

Android虚拟机的垃圾回收采用的是根搜索算法。GC会从根节点（GC Roots）开始对heap进行遍历。到最后，部分没有直接或者间接引用到GC Roots的就是需要回收的垃圾，会被GC回收掉。但是当对象不再被应用程序使用，仍然被生命周期长的对象引用，垃圾回收器无法回收,这就叫内存泄漏.

## 内存泄露的根本原因

长生命周期的对象持有短生命周期的对象。短周期对象就无法及时释放。

## 哪些情况会造成内存泄漏

- ### 错误使用单例造成的内存泄漏

- ### Handler造成的内存泄漏

- ### 线程造成的内存泄漏

- ### 非静态内部类创建静态实例造成的内存泄漏

- ### 资源未关闭造成的内存泄漏

[Android内存泄漏总结和leakcanary使用](https://www.jianshu.com/p/f55c6187a1c0)

[Detecting and fixing memory leaks in android](https://blog.mindorks.com/detecting-and-fixing-memory-leaks-in-android)