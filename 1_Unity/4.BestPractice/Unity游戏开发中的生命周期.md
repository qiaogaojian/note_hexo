# Unity游戏开发中的生命周期

## C#对象的生命周期

### 了解对象生命周期前，要先理解类、对象与引用是怎么回事。

#### 类

    是定义在代码文件中，保存在硬盘上 ，是对象的蓝本，它描述了对象在内存中大概是什么样子的。

#### 对象

    我们都知道.net将值类型存储在栈中，引用类型存储在堆中，这样做的原因是栈中的数据是轻量级的，而堆中的数据是重量级，目的是在应用程序在操作它们的时候比较方便存取，从而提高程序的运行速度。
    创建一个对象实例，用new+类名+（），就创建了一个对象实例，创建的这个对象实例是引用类型，被存储在托管堆中，以后就不用管它了，new关键字返回一个对象实例存在的地址，这个存储地址（引用）变量，被放在栈中，实际上应用程序在运行时都是操作的这个引用。

#### 引用

    上面说了，就是指 堆数据在堆中的地址,存储在栈上。

### 对象的生命周期：

在传统的非托管C++中，用构造函数创建对象实例，清除内存中的对象实例用析构函数，也就是必须要程序员手写消灭对象实例的方法，这样做的话，如果析构函数执行失败，或是由于程序员的疏忽，忘记了析构代码，那么该对象所占有的内存会一直存在内存中（直到应用程序结束），那么这样很容易造成内存资源的浪费，有效的内存空间得不到充分的利用，从而造成内存泄露。

那么在.net框架中，这样的情况将不再存在，.net中不用程序显示回收内存，自带的垃圾收集器会帮我们解决这一困扰，那么垃圾收集器是如何工作的呢？

还是先说下创建对象实例时的内存管理：实际上内存分配存在一个内存指针，它总是指向下一个对象应该放置位置，即当创建一个对象实例，看内存指针在哪里，就将这个对象实例放在指针指向的位置，然后指针再移动到下一个可以存放对象的地址，等待下一个对象的到来。

#### 创建一个对象实例要经过三步：

##### 1. 计算新的对象实例要用多少地址

##### 2. 如果堆中的地址够用，就将调用构造函数创建这个对象将把它放在内存指针指向的位置

##### 3. 返回对象的引用，并将指针指向下一个对象应该存放的地址。

#### 接下来到了垃圾回收了！

如果在计算新对象所用空间时发现空闲内在已经不够用了，那么.net就会调用垃圾收集程序做一次垃圾收集。CLR逐个排查不可达的对象，对么在托管堆中这个排查会浪费大量的时间，为了优化这个排查过程，将堆上的每一个对象对归属为某一个代中。CLR将内存中的对象分为0~2代（.net2.0中）。其中

##### 0代指的是从来没有被标记过垃圾收集的新对象（一般是函数域内的对象，被视为最先收集的对象）

##### 1代是指在上一次垃圾收集中没有被回收的对象

##### 2代是在一次以上没有被回收的对象（一般是应该程序的根级）

当内存中没有位置了，垃圾收集器就开始依次调查和收集0代对象中是否有不可达对象，如果是不可达的，就将它清除，如果是可达的对象就将它标记为1代;直到可以有足够的位置存放新对象，这时不一定所有的0代对象对被清除了，那么它们被标记为1代；（1代是否被标记为2代？）如果被0代对象全部排查过了了，还不够分配给新对象 ，那么就开始排查1代对象，没有被回收的1代对象被标记为2代。如果排查过1代对象仍然不够，就开始排查第2代，就是这样2代对象存在的时间很长。

> 注意：进行垃圾收集时.net会将正在运行的进程中的线程全部挂起，等待清理完了，再将它们释放。这样做的目的是确保应用程序在回收过程中不会访问堆。
> 无论是指类型的变量或是类类型的变量，其存储单元都是在栈中分配的，唯一不同的是类类型的变量实际上存储的是该类对象的指针，相当于vc6中的CType*,只是在.net平台的语言中将指针的概念屏蔽掉了。我们都知道栈的一大特点就是LIFO（后进先出）,这恰好与作用域的特点相对应（在作用域的嵌套层次中，越深层次的作用域，其变量的优先级越高）。因此，再出了“}”后，无论是值类型还是类类型的变量（对象指针）都会被立即释放（值得注意的是：该指针所指向的托管堆中的对象并未被释放，正等待GC的回收）。.NET中的栈空间是不归GC管理的，GC仅管理托管堆。

#### 我想就我的理解简要说明一下：

##### 1. GC只收集托管堆中的对象。

##### 2. 所有值类型的变量都在栈中分配，超出作用域后立即释放栈空间，这一点与VC6完全一样。

##### 3. 区别类类型的变量和类的对象，这是两个不同的概念。

类类型的变量实际上是该类对象的指针变量。如C#中的定义CTypemyType;与VC6中的定义CType*myType;是完全一样的，只是.net语言将*号隐藏了。与VC6相同，必须用new   关键字来构造一个对象，如(C#):CType   myType=new   CType();其实这一条语句有两次内存分配，一次是为类类型变量myType在栈中分配空间（指针类型所占的空间，对32位系统分配32位，64位系统则分配64位，在同一个系统中，所有指针类型所占的内存空间都是一样的，而不管该类型的指针所指向的是何种类型的对象），另一次是在托管堆（GC所管理的堆）中构造一个CType类型的对象并将该对象的起始地址赋给变量myType。正因为如此才造成了在同一个作用域中声明的类类型的变量和该类型的对象的生存期不一样。

## Unity Monobehaviour对象的生命周期

[Unity 生命周期:](http://docs.unity3d.com/Manual/ExecutionOrder.html)
![Unity3D 脚本生命周期](https://docs.unity3d.com/uploads/Main/monobehaviour_flowchart.svg)

### 1、静态构造函数

当程序集被加载的时候就被调用了，如果你的unity处于编辑状态时，此时你保存一个脚本（从而迫使重新编译），静态构造函数会立即被调用，因为unity加载了DLL。并且它将不会再次运行，永远只会执行一次，unity运行时，是不会再次执行了！在一个已部署的游戏上，这个构造器将会在unity加载过程的早期被调用！

### 2、非静态构造器

Unity将会在一个貌似随机的时间调用一个对象的默认构造器。当你编辑一个游戏时，在你保存一个脚本（从而迫使重新编译）后，这构造器将会被立马调用！程序运行时被随机调用多次，程序结束时也被调用了（自己测试发现）,所以不要使用构造器去初始化字段的值，unity的Awake就是专门为这个目的设计的。

### 3、Awake

只会被调用一次，在Start方法之前被调用！ 主要用于字段值的初始化工作，禁用脚本，创建游戏对象，或者 Resources.Load(Prefab) 对象

### 4、Start

只执行一次，在Awake方法执行结束后执行，但在Update方法执行前执行， 主要用于程序UI的初始化操作，比如获取游戏对象或者组件

### 5、Update

每一帧执行的，监听用户输入，播放动画，当机器忙或者性能差的时候，他会停止执行，会产生停顿的感觉，例如一个人本来在1米的位置，突然到了5米的位置上，产生了跳帧，而下面的FixedUpdate方法则相反！会一米一米的去执行！（自己调试发现，Update是先于OnGUI执行的，且执行一次Update之后，会执行两次OnGUI）

### 6、FixedUpdate

不管当前机器忙不忙，都会保证每一帧执行一次！避免跳帧！固定更新。固定更新常用于移动模型等操作。

### 7、LateUpdate  

先执行Update，然后才去执行lateUpdate(Update方法执行完，必定接着执行LateUpdate，而Update和FixedUpdate方法的执行顺序不确定，而且有时候FIxedUpdate执行了多帧，而Update却只执行了一帧，这就是因为跳帧的缘故造成的（取决于你的机器性能）！)，如果现在有100个脚本，分别有100个 Update()函数，其中只有一个LateUpdate，那么在同一帧中，等待100个Update()执行完后，才执行这一个LateUpdate()。

### 8、OnGUI

在这里面进行GUI的绘制，且GUI是每帧擦除重绘的！仅仅只是绘制！没有生命周期的概念！所有关于绘制GUI的代码，都要直接或者间接地写到OnGUI方法中！

### 9、OnDestroy

当前脚本销毁时调用

### 10、OnEnable

脚本可用时被调用、如果脚本是不可用的，将不会被调用！

### 11、OnDisable

如果脚本被设置为不可用将会被执行，程序结束时也会执行一次！

**OnEnable** 和 **OnDisable** 只受脚本的可用状态的影响（enabled）,而 **OnBecameVisible** 和 **OnBecameInvisible** 是受对象是否可见的影响！即使脚本设置为不可用，**OnBecameVisible** 和 **OnBecameInvisible** 也会被执行，主要是看对象是否在场景中显示了！

### 脚本执行顺序总结：

假如现在有三个GameObject对象：a1 > a2 > a3 (a1为a2的父节点，a2为a1的父节点，unity执行脚本的顺序是从上往下执行，也就是说先执行父节点上的脚本，再去执行子节点的脚本，子节点上如果有多个脚本，那么也是自上而下的顺序执行)，这三个对象对应各有一个脚本：s1，s2，s3，且这三个脚本代码都一样，都有

```C#
Awake
Start
Update
LateUpdate
FixUpdate
```

那么当运行程序时，程序会进行分组，即把s1，s2，s3中的Awake方法组成一组，把Start方法组成一组，把Update方法组成一组，把LateUpdate方法组成一组，把FixUpdate方法组成一组，最后按照Awake，Start，FixUpdate，Update，LateUpdate（FixUpdate和Update顺序不确定）的顺序依次执行！

即把Awake组里面的Awake方法全执行完，再去依次执行Start，FixUpdate，Update，LateUpdate组里面的代码：执行顺序如下：

```C#
Awak1 Awak2 Awak3

Start1 Start2 Start3

FixUpdate1 FixUpdate2 FixUpdate3

Update1 Update2 Update3

LateUpdate1 LateUpdate2 LateUpdate3
```

![image.png](http://upload-images.jianshu.io/upload_images/3947109-fdee82c2071ad1a4.png)
![image.png](http://upload-images.jianshu.io/upload_images/3947109-41683889d1bd3943.png)
![image.png](http://upload-images.jianshu.io/upload_images/3947109-4821e9ab5232be2d.png)

#### 例子

如果有三个对象，a1 > a2 > a3 （父子级的关系），挂有三个脚本s1，s2，s3，三个脚本都有

```C#
Awake,Start,OnEnable,OnDisable,Update
```

方法，那么unity执行的顺序为：

```C#
awake1
OnEnable1
awake2
OnEnable2
awake3
OnEnable3
Start1
Start2
Start3
Update1
Update2
Update3
```

如果在脚本s2的Awake方法中设置脚本s1不可用（s1.enabled=false），那么脚本的执行结果为：

```C#
awake1
OnEnable1
OnDisable1
awake2
OnEnable2
awake3
OnEnable3
Start2
Start3
Update2
Update3
```

如果在脚本s2的Awake方法中设置脚本s3不可用（s3.enabled=false），那么脚本的执行结果为：

```C#
awake1
OnEnable1
awake2
OnEnable2
awake3
Start1
Start2
Update1
Update2
```

如果在脚本s2的Start方法中设置脚本s3不可用（s3.enabled=false），那么脚本的执行结果为：

```C#
awake1
OnEnable1
awake2
OnEnable2
awake3
OnEnable3
Start1
OnDisable3
Start2
Update1
Update2
```

> 总结：关键是看设置脚本不可用的位置：

##### 父级操作子级

###### 1. 如果在父级的Awake方法里面设置子级的脚本不可用

    那么仅仅只会执行子级里面的Awake方法，当子级被激活后（enabled=true），会先执行OnEnable，然后执行Start方法，Update等帧序列方法都会开始执行

###### 2. 如果在父级的Start方法里面设置子级的脚本不可用

    那么会执行子集里面的Awake，OnEnable和OnDisable方法，当子级被激活后（enabled=true），会先执行OnEnable，然后执行Start方法，Update等帧序列方法也都会开始执行

##### 子级操作父级

###### 1. 如果在子级的Awake方法里面设置父级的脚本不可用

    那么会执行父级里面的Awake，OnEnable和OnDisable方法，当父级被激活后（enabled=true），会先执行OnEnable，然后执行Start方法，Update等帧序列方法都会开始执行

###### 2. 如果在子级的Start方法里面设置父级的脚本不可用

    那么会执行父集里面的Awake，OnEnable，Start和OnDisable方法，当父级被激活后（enabled=true），会先执行OnEnable，Update等帧序列方法也都会开始执行

> 如果被激活的脚本之前没有调用Start方法，那么当此脚本被激活后，会调用一次Start方法！具体，看生命周期第一幅图，在文章后面！

##### OnBecameVisible 和 OnBecameInvisible

###### 1. 当一开始加载一个对象时：

|   Game       |Scene          |  OnBecameInvisible         |       OnBecameInvisible  |
|--------------|---------------|----------------------------|--------------------------|
|Game 显示     |Scene 显示      |OnBecameInvisible   不执行  |OnBecameInvisible   执行   |
|Game 不显示   |Scene 不显示    |OnBecameInvisible   不执行   |OnBecameVisible 不执行     |
|Game 不显示   |Scene 显示      |OnBecameInvisible   不执行   |OnBecameVisible 执行       |
|Game 显示     |Scene 不显示    |OnBecameInvisible   不执行   |OnBecameVisible 执行       |

> 小结：只要Game和Scene中有一个显示了，OnBecameVisible 就会执行！而OnBecameInvisible 一直都不会执行

###### 2. 当移动对象时：

    game 和 scene 对象必须在两个场景中同时消失  OnBecameInVisible 才执行, scene 和 game 只要有一方进入了场景 OnBecameVisible 就执行了.

####### test1

######## test2
