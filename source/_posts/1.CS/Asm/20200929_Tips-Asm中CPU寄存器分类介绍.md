# CPU寄存器分类介绍

32位CPU所含有的寄存器有:
4个数据寄存器(EAX,EBX,ECX,EDX)
2个变址和指针寄存器(ESI,EDI) 2个指针寄存器(ESP,EBP)
6个段寄存器(ES,CS,SS,DS,FS,GS)
1个指令指针寄存器(EIP)1个标志寄存器(EFlags)

## 一.数据寄存器

|寄存器|作用|
|-|-|
|EAX|累加器(Accumulator),用累加器进行的操作可能需要更少时间.可用于乘,除,输入,输出等操作,使用频率很高|
|EBX|基地址寄存器(Base Register),它可作为存储器指针来使用|
|ECX|计数寄存器(Count Register),在循环和字符操作时,用它控制循环次数,在位操作中,当移多位时,要用CL来指明移位的位数|
|EDX|数据寄存器(Data Register),在进行乘除运算时,它可作为默认的操作数参与运算,也可用于存放IO的端口地址

## 变址寄存器

|寄存器|作用|
|-|-|
|ESI|主要用于存放存储单元的偏移量|
|EDI|主要用于存放存储单元的偏移量|

## 指针寄存器

|寄存器|作用|
|-|-|
|EBP|为基指针(Base Pointer)寄存器,用它可直接存取堆栈中的数据|
|ESP|为堆栈指针(Stack Pointer)寄存器,用它只可访问栈顶

## 段寄存器

|寄存器|作用|
|-|-|
|ECS|代码段寄存器(Code Segment Register),其值为代码段的段值|
|EDS|数据段寄存器(Data Segment Register),其值为数据段的段值|
|EES|附加段寄存器(Extra Segment Register),其值为附加数据段的段值|
|ESS|堆栈段寄存器(Stack Segment Register),其值为堆栈段的段值|
|EFS|附加段寄存器(Extra Segment Register),其值为附加数据段的段值|
|EGS|附加段寄存器(Extra Segment Register),其值为附加数据段的段值|

## 指令指针寄存器

|寄存器|作用|
|-|-|
|EIP|是存放下次将要执行的指令在代码段的偏移量|

## 标志寄存器

|寄存器|作用|
|-|-|
|EFlags|进位标志,奇偶标志,辅助进位标志,零标志,符号标志,溢出标志/追踪标志,中断允许标志,方向标志/特权标志,嵌套任务标志,重启动标志,虚拟8086方式标志VM

## 参考链接

[汇编各种寄存器介绍](https://www.cnblogs.com/wisehead/articles/3819233.html)