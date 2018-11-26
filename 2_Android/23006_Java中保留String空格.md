# Java中保留String空格

## 有空格的地方用 \u0020 代替

```java
String msg = MessageFormat.format("您是否要花费\u0020\u0020\u0020{0}\u0020\u0020\u0020钻石\n" +
                                                                "点亮一个月的{1}徽章权限？", price, name);
```

## 参考链接

[How to keep the spaces at the end and/or at the beginning of a String?
](https://stackoverflow.com/questions/1587056/how-to-keep-the-spaces-at-the-end-and-or-at-the-beginning-of-a-string)