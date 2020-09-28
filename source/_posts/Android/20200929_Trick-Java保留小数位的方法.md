# Java保留小数位的方法

## 方法一

```java
(double) (Math.round(sd3*10000)/10000.0);
```

``` java
private String modelNum(int num)
{
    if (num>=10000)
    {
        double result = num/10000.0;
        result = Math.round(result*10)/10.0;
        return result + "万";
    }
    else if(num>=100000000)
    {
        double result = num/100000000.0;
        result = Math.round(result*10)/10.0;
        return result + "亿";
    }
    return num+"";
}
```

## 方法二

```java
import java.text.DecimalFormat;

DecimalFormat df2 = new DecimalFormat("###.00");
DecimalFormat df2 = new DecimalFormat("###.000");
System.out.println(df2.format(doube_var));
```

## 方法三

```java
String.format("%10.2f%%", doube_var);
```
