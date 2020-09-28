# C#中enum 和string int的互转

```C#
enum Colors  
{
    Red,
    Green,
    Blue,
    Yellow
}
```

## Enum-->String

### 利用 Object.ToString()方法：

```C#
Colors.Green.ToString()=="Green";
```

### 利用 Enum 的静态方法 GetName 与 GetNames：

```C#
Enum.GetName(typeof(Colors),3))
== Enum.GetName(typeof(Colors), Colors.Blue))
== "Blue";

Enum.GetNames(typeof(Colors))
== { "Red",    "Green",    "Blue",     "Yellow" };
```

## String-->Enum

### 利用 Enum 的静态方法 Parse：

```C#
(Colors)Enum.Parse(typeof(Colors), "Red")
```

## Enum-->Int

### 因为枚举的基类型是除 Char 外的整型，所以可以进行强制转换。

```C#
(int)Colors.Red == 0;
(byte)Colors.Green == 1;
```

## Int-->Enum

### 可以强制转换将整型转换成枚举类型。

```C#
Colors color = (Colors)2 ;
color == Colors.Blue;
```

### 利用 Enum 的静态方法 ToObject。

```C#
Colors color = (Colors)Enum.ToObject(typeof(Colors), 2);
color == Colors.Blue
```

## 判断某个整型是否定义在枚举中的方法：Enum.IsDefined

```C#
Enum.IsDefined(typeof(Colors), 3)) == true;
```
