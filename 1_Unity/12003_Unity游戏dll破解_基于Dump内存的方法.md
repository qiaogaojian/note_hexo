# Unity游戏dll破解_基于Dump内存的方法

## windbg

```sh
.dump /ma E:/dumps/myapp.dmp
.load psscor4
!SaveAllModules E:\dumps\modules

```

## Dll Memory Dumper

- 打开游戏
- 打开DllMemoryDumper
- 输入游戏进程编号按Enter键
