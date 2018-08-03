# mysql设置字符集

> 一般来说, utf8 最为通用,所以字符集设置为 uft8

- 修改mysql配置文件 my.ini 中所有其他字符改为 utf8

- mysql控制台下运行以下命令

```sql
mysql> set names utf8;
mysql> alter database test character set utf8;
```

- 重启mysql 这时数据库的编码就是utf8了