# Java中StringFormat和MessageFormat的区别

## MessageFormat.format()

format string accepts argument positions (eg. {0}, {1}). Example:

```java
MessageFormat.format("This is year {0}!",1992)
```

The developer doesn't have to worry about argument types, because they are, most often, recognized and formated according to current Locale.

## String.format()

format string accepts argument type specifiers (eg. %d for numbers, %s for strings). Example:

```java
String.format("This is year %d!")
```

String.format() generally gives you much more control over how the argument is displayed thanks to many options you can specify with the type specifier. For instance, format string "%-6.2f" specifies to display a left-aligned floating point number with min. width 6 chars and precision of 2 decimal places.

## ref

[difference between MessageFormat.format and String.format in jdk1.5?
](https://stackoverflow.com/questions/2809633/difference-between-messageformat-format-and-string-format-in-jdk1-5)