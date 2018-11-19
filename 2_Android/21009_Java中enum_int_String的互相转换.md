# Java中enum/int/String的互相转换

## enum 和 int

### enum -> int

```java
int i = enumType.value.ordinal();
```

### int -> enum

```java
enumType b = enumType.values()[i];
```

## enum 和 string

### enum -> string

```java
s = enumType.name();
```

### string -> enum

```java
enumType b = enumType.valueOf(s);
```