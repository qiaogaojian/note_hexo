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