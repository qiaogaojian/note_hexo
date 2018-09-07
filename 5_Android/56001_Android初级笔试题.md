# Java语言程序设计

## 选择题

下列各题A）、B）、C）、D）四个选项中，只有一个选项是正确的。

### 1、下列运算符中，优先级最高的是（C）

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

-   继承

    继承是指在定义和实现一个类的时候,可以在一个已经存在的类的基础上来进行,把这个已经存在的类定义的内容作为自己的内容,并可以加入若干新的内容,或者重写之前的方法使之更加符合需要.

    继承是子类自动共享父类数据和方法的机制,这是类的一种关系,提高了软件的可重用性和可扩展性.

-   多态

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

```



## 笔试题

### 1、Android四大组件都是什么？并简述其作用。

### 2、Activity的生命周期。

### 3、启动Activity不同模式的区别（standard、singleTop、singleTask、singleInstence）。

### 4、Android中的动画有哪些，简述其内容及优缺点。

### 5、Android序列化都有哪些，简述优缺点。

### 6、写出了解的第三方框架（如Glide、otto等）及其作用。

### 7、用RxJava1或者RxJava2实现“每隔1秒执行一次，一共执行5次”（可选）