---
title: Android Databinding 报错 Index -1 out of bounds for length
date: 2023-11-21 18:00:43
categories: ['5.技能', '问题记录']
tags: ['android', '技能', '问题记录']
---
  
  
## 问题

新建布局后打包编译出错:
![](/images/abf0554391db58ba13e6b783dd284f9.jpg)
  
  
## 分析

  
  
### 出错代码

`XmlEditor.java`
![](/images/Pasted image 20230130161512.png)
  
  
### 测试

```java
public class HelloWorld {
    public static void main(String[] args) {
        String line1 = "<?xml version=\"1.0\" encoding=\"utf-8\"?><!--15710579216-->";
        int index = line1.lastIndexOf("</");
        System.out.println(index);
    }
}
```
  
  
## 解决

Debug 发现 node.getStop().line == 1, 也就是文件内容的最后一行是第一行, 但是这个文件实际上是113行, 由此得出结论: 该程序没有识别出文件的换行.

![](/images/Pasted image 20230130165042.png)
使用 notepad++ 检查问题文件的换行编码, 是 CR

![](/images/Pasted image 20230130165211.png)
更改为 CRLF, 问题解决
