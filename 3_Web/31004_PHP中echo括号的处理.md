# PHP中echo括号的处理

## 关于echo

echo 不是一个函数（它是一个语言结构）， 因此你不一定要使用小括号来指明参数，单引号，双引号都可以。 echo （不像其他语言构造）不表现得像一个函数， 所以不能总是使用一个函数的上下文。 另外，如果你想给echo 传递多个参数， 那么就不能使用小括号。

## 测试代码

```php
//1、全部用括号包起来的情况，其中只能存在一个参数，才会被输出
echo ('b','#',"aabbcc");        //报错 "语法错误，不被期望的','"(相当于传了多个参数)
echo ('b'.'#'."aabbcc");        //b#aabbcc
echo ((('abc')));               //abc

// 2、无","或"."连接，但存在括号的情况会被解析为函数的调用
// 注意：如果存在连续三个及三个以上括号的情况，则会报错
echo "str_replace"('b','#',"aabbcc");   //aa##cc
echo 'strlen'('abc');                   //3
echo ('strlen')('abc');                 //3
echo ('aaa')('bbb');                    //报错：aaa函数未定义
echo ('strlen')('abc')('ss');           //报错 "函数名不是一个字符串"

//3、存在","或"."连接的情况，即单纯的字符串顺序输出或连接
echo 'strlen',("aaa");  //strlenaaa
echo 'strlen'.("aaa");  //strlenaaa
echo ('aaa').('bbb');   //aaabbb
```