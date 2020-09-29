---
description: Unity中sqlite数据库的使用
---
# Unity中sqlite数据库的使用

## 创建数据库

进入sqlite命令行工具所在的目录,打开命令行,创建一个名称为test.db的数据库

```sql
sqlite3 test.db
```

## 使用已有数据库

使用已有数据库和创建数据库命令一样 sqlite3 + 数据库名字

## 显示当前数据库

```sql
.databases
```

## 显示当前数据表

```sql
.tables
```

## 格式化输出

```sql
sqlite>.header on
sqlite>.mode column
sqlite>.timer on
```

## 创建数据表

```sql
sqlite> CREATE TABLE COMPANY(
   ID INT PRIMARY KEY     NOT NULL,
   NAME           TEXT    NOT NULL,
   AGE            INT     NOT NULL,
   ADDRESS        CHAR(50),
   SALARY         REAL
);
```

## 删除数据表

```sql
sqlite>DROP TABLE COMPANY;
```

## 修改数据表

```sql
-- 插入表
INSERT INTO TABLE_NAME [(column1, column2, column3,...columnN)]
VALUES (value1, value2, value3,...valueN);

-- 更新表
sqlite> UPDATE COMPANY SET ADDRESS = 'Texas' WHERE ID = 6;
```

## 查询数据表

```sql
SELECT column1, column2, columnN FROM table_name;
```

## 操作记录

```sh
ETA@QiaoGaojian-PC MINGW64 /d/gitee/etaframe (develop)
$ cd sqlitetool

ETA@QiaoGaojian-PC MINGW64 /d/gitee/etaframe/sqlitetool (develop)
$ ls
sqldiff.exe  sqlite3.exe  sqlite3_analyzer.exe  test.db

ETA@QiaoGaojian-PC MINGW64 /d/gitee/etaframe/sqlitetool (develop)
$ sqlite3 test.db
SQLite version 3.8.10.2 2015-05-20 18:17:19
Enter ".help" for usage hints.
sqlite> .databases
seq  name             file
---  ---------------  ----------------------------------------------------------
0    main             D:\gitee\etaframe\sqlitetool\test.db
sqlite> .tables;
Error: unknown command or invalid arguments:  "tables;". Enter ".help" for help
sqlite> .tables
user
sqlite> select * from user;
1|Michael|18
sqlite> insert into user values
   ...> (2,"Michelle",19);
sqlite> select * from user;
1|Michael|18
2|Michelle|19
sqlite> .header on
sqlite> select * from user;
id|name|age
1|Michael|18
2|Michelle|19
sqlite> .mode column
sqlite> .timer on
sqlite> select * from user;
id          name        age
----------  ----------  ----------
1           Michael     18
2           Michelle    19
Run Time: real 0.004 user 0.000000 sys 0.000000
sqlite> exit
   ...> ;
Run Time: real 0.001 user 0.000000 sys 0.000000
Error: near "exit": syntax error
sqlite> .exit

ETA@QiaoGaojian-PC MINGW64 /d/gitee/etaframe/sqlitetool (develop)
$ sqlite3 test.db
SQLite version 3.8.10.2 2015-05-20 18:17:19
Enter ".help" for usage hints.
sqlite> .status
Error: unknown command or invalid arguments:  "status". Enter ".help" for help
sqlite> .show
        echo: off
         eqp: off
  explain: off
     headers: off
        mode: list
   nullvalue: ""
      output: stdout
colseparator: "|"
rowseparator: "\n"
       stats: off
       width:
sqlite> select * from user;
1|Michael|18
2|Michelle|19
sqlite> .header on
sqlite> .mode column
sqlite> select * from user;
id          name        age
----------  ----------  ----------
1           Michael     18
2           Michelle    19
sqlite> .timer on
sqlite> select * from user;
id          name        age
----------  ----------  ----------
1           Michael     18
2           Michelle    19
Run Time: real 0.005 user 0.015625 sys 0.000000
sqlite> sqlite3 test.db .dump > test.sql
   ...> ;
Run Time: real 0.000 user 0.000000 sys 0.000000
Error: near "sqlite3": syntax error
sqlite> .exit

ETA@QiaoGaojian-PC MINGW64 /d/gitee/etaframe/sqlitetool (develop)
$
```