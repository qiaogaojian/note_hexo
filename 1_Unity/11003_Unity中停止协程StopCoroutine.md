# Unity中停止协程StopCoroutine

## 必须用字符串启动协程才能停止协程

```C#
//要想停止协程,必须用字符启动协程
StartCoroutine("FunctionName");
//如果要传参
StarCoroutine("FunctionName",object);
```

## 停止协程

```C#
//停止协程
StopCoroutine("FunctionName");
//停止所有协程(慎用)
StopAllCoroutine();
```