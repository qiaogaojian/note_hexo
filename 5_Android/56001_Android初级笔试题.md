# Java语言程序设计

## 选择题

下列各题A）、B）、C）、D）四个选项中，只有一个选项是正确的。

### 1、下列运算符中，优先级最高的是（A）

A）++
B）+
C）*
D）>

-   ##### 解析:

    口诀:

    **单目双目位关系 逻辑三目后赋值**

    单目: 单目运算符  -, ++, --

    双目:双目运算符   *, /, %, +, -

    位:    位运算符      <<, >>

    关系: 关系运算符 >, <, >=, <=, ==, !=

    逻辑: 逻辑运算符 &&, ||, &, |, ^

    三目: 三目运算符 ? :

    赋值: 赋值运算符 =

    ​

### 2、heap（堆）处理方式正确的是（A）

A）先进先出
B）后进先出
C）先进后出
D）后进后出

- ##### 解析:

  数据结构:

  ​	堆: 完全二叉树

  ​	栈: 后进先出

  内存:

  ​	堆: 先进先出

  ​	栈: 后进先出

  ​

### 3、计算3<<3的值为（24）

A）8
B）9
C）12
D）31

-   ##### 解析

    x左移n ==> x * 2^n

    x右移n ==> x  / 2^2

    无正确答案, 正确答案为 24.

    ​

### 4、下列运算中属于Java跳转语句的是（D）

A）goto
B）try
C）sleep
D）break

-   ##### 解析

    Java跳转语句有:

    1. continue

    2. break

    3. return

       ​

### 5、设有学生与课程两个实体，每个学生可以选修多门课程，每门课程可以被多个学生选修，则学生与课程实体间的联系是（C）

A）1:m
B）m:1
C）m:n
D）1:1



## 简答题

### 1、修饰符“public/private/protected/缺省的修饰符”的作用域？

> Java访问修饰符包括 private,default,protected,public四个访问修饰符

作用域如下:

- private  	    	 当前类

- default            当前包

- protected       当前包,不同包中继承当前类的子类

- public              所有类

  ​


### 2、String,StringBuffer,StringBuilder的区别

-   执行效率

    StringBuilder > StringBuffer > String

    StringBuilder 和 StringBuffer 是字符串变量, String 是字符串常量,一旦创建不可更改.

- 线程安全

  StringBuilder 是线程不安全的, StringBuffer String是线程安全的, 单线程用StringBuilder效率更高,多线程用StringBuffer.

- 适用场景

  String: 		 操作较少的数据.

  StringBuilder: 单线程大量操作数据.

  StringBuffer:   多线程大量操作数据.

  ​


### 3、ArrayList、LinkedList、Vector的区别



| :List      | 实现方式 | 线程安全                   | 效率                             | 遍历        |
| ---------- | ---- | ---------------------- | ------------------------------ | --------- |
| ArrayList  | 数组   | 非线程安全                  | 遍历和随机访问效率高,查询快,在尾部添加效率高        | 选for      |
| Vector     | 数组   | 线程安全, 方法加了synchronized |                                |           |
| LinkedList | 双向链表 | 非线程安全                  | 插入删除数据效率比较高,  在头部添加快很多,删除数据快很多 | 选Iterator |



### 4、简述HashMap的特性以及底层实现原理

- 实现原理

  java编程语言中,最基本的结构就两种,数组和模拟指针, 所有的数据结构都可以用这两个基本机构来构造, HashMap实际上是一个"链表散列"的数据结构, 即数组和链表的结合体.

  HashMap底层就是一个数组结构,数组中每一项又是一个链表,新建一个HashMap时,就会初始化一个数组.

- 特性

  HashMap是基于Map接口的实现,存储的是键值对,可以接受null键和值,而hashtable不能

  HashMap是非线程安全的,单线程下HashMap的性能更高

  put(key,value) 设置值  get(key) 取值

  ​

### 5、Java是如何实现跨平台的？

​	java源代码.java文件经过编译器编译后生成字节码.class文件, 然后再由java虚拟机解释执行字节码,生成不同机器可以识别的机器码,由于java虚拟机是跨平台的,所以实现了java代码在不同平台上的运行.



### 6、面向对象的三大要素是什么？简述其主要内容

-   封装

    封装是为了隐藏内部实现细节,是保证软件具有良好模块性的基础. 封装的目标就是要实现软件模块"高内聚,低耦合",防止程序之间的互相依赖.

- 继承

    继承是指在定义和实现一个类的时候,可以在一个已经存在的类的基础上来进行,把这个已经存在的类定义的内容作为自己的内容,并可以加入若干新的内容,或者重写之前的方法使之更加符合需要.

    继承是子类自动共享父类数据和方法的机制,这是类的一种关系,提高了软件的可重用性和可扩展性.

- 多态

    多态是运行时刻接口匹配的对象相互替换的能力. 程序定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编译器不确定,而是在运行期间才确定, 因此这样就可以使得引用变量绑定到各种不同的类实现上,从而实现不同的行为. 多态性增强了软件的灵活性和扩展性.

    ​

### 7、实现一个最优单例模式。

```java
public class SingletonTest
{
  	private SingletonTest()
    {
    }

  	private static class Inner
    {
    	static SingletonTest singletonTest = new SingletonTest();
    }

  	public static SingletonTest GetInstance()
    {
    	return Inner.singletonTest;
    }
}
```



### 8、实现一个计数器，在n秒内每间隔m秒执行一次，一共执行了多少次。要求：1、间隔m秒执行一次；2、共n秒。注：m、n为动态参数。

```java
public class CountNumber {
    private int n;
    private int m;

    public CountNumber(int n, int m) {
        this.n = n;
        this.m = m;
    }

    public int GetCount(int n, int m) {
        int countNum = 0;
        countNum = n / m;
        return countNum;
    }

    public int GetCount() {
        int countNum = 0;
        countNum = this.n / this.m;
        return countNum;
    }
}
```



## 笔试题

### 1、Android四大组件都是什么？并简述其作用。

-   Activity

    ​	Activity是Android程序与用户交互的窗口, 是Android构造块中最基本的一种, 他需要保持各界面的状态, 做很多持久化的事情, 妥善管理生命周期以及一些跳转逻辑.

    ​

- Service

    ​	Service后台服务于Activity, 封装一个完整的功能逻辑实现, 接收上层指令, 完成相关的事物.

    ​

- ContentProvider

    ​	ContentProvider是Android提供的第三方应用数据的访问方案, 可以派生ContentProvider类,对外提供数据, 可以像数据库一样进行选择排序, 屏蔽内部数据的存储细节, 向外提供了统一的接口模型, 大大简化上层应用,对数据整合提供了更方便的途径.

    ​

- BroadCast Receiver

    ​	BroadCast Receiver接受一种或者多种Intent作为触发事件, 接收相关消息,做出一些简单处理,转换成一条Notification, 统一了Android的事件广播模型



### 2、Activity的生命周期。



![](https://upload-images.jianshu.io/upload_images/3994917-019104c9fc5cb373.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/513/format/webp)

##### 完整生存期

活动在onCreate( )方法和onDestroy( )方法之间所经历的,就是完整生存期,一般情况下,一个活动会在onCreate( )方法中完成各种初始化操作,而在onDestroy( )方法中完成释放内存的操作.

##### 可见生存期

活动在onStart( )方法和onStop方法之间所经历的,就是可见生存期.在可见生存期内,活动对于用户总是可见的,即便有可能无法和用户进行交互.我们可以通过这两个方法,合理地管理哪里对用户可见的资源.比如在onStart( )方法中对资源进行加载,而在onStop( )方法中对资源进行释放,从而保证处于停止状态的活动不会占用过多的内存.

##### 前台生存期

活动在onResume( )方法和onPause( )方法之间所经历的就是前台生存期.在前台生存期内,活动总是处于运行状态的,此时的活动是可以和用户进行交互的,我们平时看到的和接触最多的也就是这个状态下的活动.

- onCreate( )

  在活动第一次被创建的时候被调用,这个方法一般用来初始化操作,比如加载布局,绑定事件等.

- onStart( )

  在活动由不可见变为可见的时候调用.

- onResume( )

  在活动准备好和用户进行交互的时候调用,此时的活动一定位于返回栈的栈顶,并且处于运行状态.

- onPause( )

  在系统准备去启动或者恢复另一个活动的时候调用,通常会在这个方法种将一些消耗CPU的资源释放掉,以及保存一些关键数据,但这个方法的执行速度一定要快,不然会影响到新的栈顶活动的使用.

- onStop( )

  这个方法在活动完全不可见的时候调用,它和onPause( )方法的主要区别在于,如果启动的新活动是一个对话框式的活动,那么onPause( )方法会得到执行,而onStop( )方法并不会执行.

- onRestart( )

  在活动由停止状态变为运行状态之前调用,也就是活动被重新启动了.

- onDestroy( )

  在活动被销毁之前调用,之后活动的状态将变为销毁状态.



### 3、启动Activity不同模式的区别（standard、singleTop、singleTask、singleInstence）。

活动的启动模式一共有4种,分别为 standard, singleTop, singleTask, singleInstance, 可以在AndroidMainfest.xml中通过给<activity>标签指定android:launchMode属性来选择启动模式.

- standard

  Android的默认启动模式,这种模式下,Activity可以有多个实例,每次启动Activity,无论任务栈中是否已经有这个Activity的实例,系统都会创建一个新的Activity实例.

- singleTop

  singleTop和standard模式非常相似,主要区别就是当一个singleTop模式的Activity已经位于任务栈的栈顶,再去启动它时,不会在创建新的实例,如果不位于栈顶,就会创建新的实例. 位于栈顶时启动会调用onNewIntent( )函数 .

- singleTask

  singleTask模式的Activity在同一Task内只有一个实例,如果Activity已经位于栈顶,系统不会创建新的Activity实例,和singleTop模式一样.但Activity已经存在但不位于栈顶时,系统就会把该Activity移动到栈顶,并把它上面的Activity出栈,singleTask是Task内单例的,需要单例才会设置这个模式.

- singleInstance

  也是单例,和singleTask不同的是,singleTask只是任务栈内单例,系统是可以有多个singleTask Activity实例的,而singleInstance Activity在整个系统中只有一个实例,启动singleInstance Activity时,系统会创建一个新的任务栈,并且这个任务栈只有他一个Activity.

### 4、Android中的动画有哪些，简述其内容及优缺点。

- Tween Animation:

  补间动画,通过对场景中的对象不断做图像变换(平移,旋转,缩放,透明度)产生的动画效果,是一种渐变动画.

  - 支持4种类型:平移,旋转,缩放,不透明

  - 只是显式的位置变动,View的实际位置未改变,表现为View移动到其他地方,点击事件仍在原处才可响应

  - 组合使用步骤较复杂

  - ViewAnimation也是指此动画

    优点:相对于帧动画,补间动画更加连贯自然

    缺点: 当平移动画执行完停止最后的位置,结果焦点还在原来的位置,控件属性未改变

- Frame Animation:

  帧动画,顺序播放事先做好的图像,是一种画面转换动画.

  - 用于生成练习的gif动画

  - Drawable Animation也是指此动画

    优点:制作简单

    缺点:效果单一,逐帧播放需要很多图片,占用空间较大.

- Property Animation:

  属性动画,通过动态的改变对象的属性从而达到的动画效果.Android(api11) 新特性.

  - 支持对所有View能更新的属性的动画,需要属性的set/get方法

  - 更改的是View的实际属性,不影响其动画执行后所在位置的正常使用

  - Android3.0 (API11)及以后的功能

    优点:易定制,效果强

    缺点:向下兼容的问题

### 5、Android序列化都有哪些，简述优缺点。

- Serializable(Java自带):

  Serializable是序列化的意思,表示将一个对象转换成可存储或可传输的状态. 序列化后的对象可以在网络上进行传输,也可以存储到本地.

- Parcelable(Android专用):

  也可以实现相同的效果,不过不同于将对象进行序列化,Parcelable的实现原理是将一个完整的对象进行分解,而分解后的每一部分都是Intent所支持的数据类型,这样也就实现传递对象的功能了.

  ##### 区别

  Parcelable比Serializable性能高,所以应用内传递数据推荐使用Parcelable,但是Parcelable不能使用在要将数据存储在磁盘上的情况,因为在外界有变化的情况下,Parcelable不能很好的保证数据的持续性.

### 6、写出了解的第三方框架（如Glide、otto等）及其作用。



### 7、用RxJava1或者RxJava2实现“每隔1秒执行一次，一共执行5次”（可选）



























