---
title: 深入浅出 python 闭包
date: 2022-11-12 00:42:56
categories: ['5.技能', '编程语言', 'Python']
tags: ['Python', '技能', '编程语言', 'python']
---

>原文地址 [zhuanlan.zhihu.com](https://zhuanlan.zhihu.com/p/22229197)

周围有些同事初学 python，往往对 python 的一些高级特性，比如生成器 (Generator), 闭包(closure)，装饰器(Decorator) 感到有点不太容易理解，虽然这些特性并非 python 独有，但真的掌握了一定会让你感觉原来生活如此美好。
  
  
## 1.  闭包介绍

闭包概念：在一个内部函数中，对外部作用域的变量进行引用，(并且一般外部函数的返回值为内部函数)，那么内部函数就被认为是闭包。举个栗子先：

![](https://pic3.zhimg.com/0bf070da6fba4187510d4f423f451dd2_b.png)![](https://pic4.zhimg.com/564bc6ce56fc4cf2bf5128cd6c60b477_b.png)![](https://pic1.zhimg.com/dee4a1e824a6da024b5eace407eeab90_r.jpg)![](https://pic1.zhimg.com/f54f1765811dd5c13f415ec25cf2072c_b.png)![](https://pic2.zhimg.com/5ea23ecb27f7aa70eff3b6e445093f31_b.png)
  
  
## 2. 常见错误

  
  
###  闭包无法修改外部函数的局部变量

![](https://pic1.zhimg.com/37066f1c440ecf7a31f685728119cfa8_r.jpg)
这个是什么意思呢？
如果 innerFunc 可以修改 x 的值的话，x 的值前后会发生变化，但结果是：

![](https://pic3.zhimg.com/a7d6feca997ecb0262aaa2875a77c4fa_b.png)
  
  
### python 循环中不包含域的概念

![](https://pic3.zhimg.com/0c44b9dee2b981816d4137a432c4185e_b.png)

按照大家正常的理解，应该输出的是 0, 2, 4 对吧？但实际输出的结果是: 4, 4, 4. 原因是什么呢？loop 在 python 中是没有域的概念的，flist 在像列表中添加 func 的时候，并没有保存 i 的值，而是当执行 f(2) 的时候才去取，这时候循环已经结束，i 的值是 2，所以结果都是 4。

其实修改方案也挺简单的：

![](https://pic1.zhimg.com/b61fccab921e91f626fb0488a0bace5c_r.jpg)
  
  
## 3. 闭包的作用

闭包可以保存当前的运行环境，以一个类似棋盘游戏的例子来说明。假设棋盘大小为 50*50，左上角为坐标系原点 (0,0)，我需要一个函数，接收 2 个参数，分别为方向 (direction)，步长 (step)，该函数控制棋子的运动。 这里需要说明的是，每次运动的起点都是上次运动结束的终点。

参考代码：

![](https://pic3.zhimg.com/9ce54b9dd939bb31e18923e7b3618d42_b.png)![](https://pic3.zhimg.com/dcbee56a8bcc7d164dddd39d68ee80b6_b.png)

当然，闭包在爬虫以及 web 应用中都有很广泛的应用，并且闭包也是装饰器的基础，这些内容笔者会在后续的文章中分别介绍，这里就不多谈了。理解了本文中的概念，你应该知道的关于闭包的知识也差不多了，请在自己的编程中尽情使用吧。

**参考连接**：

1.  [Closure (computer programming)](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Closure_%28computer_programming%29)  
    
2.  [About python closure](https://link.zhihu.com/?target=http%3A//stackoverflow.com/questions/11408515/about-python-closure)  
    
3.  [Python 中的闭包实例详解_python_脚本之家](https://link.zhihu.com/?target=http%3A//www.jb51.net/article/54498.htm)