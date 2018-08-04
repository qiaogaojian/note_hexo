# mysql设置字符集

- 查看当前数据库字符集

    ```sql
    mysql> show variables like "%char%";

    //输出
    +--------------------------+--------------------------------+
    | Variable_name            | Value                          |
    +--------------------------+--------------------------------+
    | character_set_client     | utf8                           |
    | character_set_connection | latin1                         |
    | character_set_database   | latin1                         |
    | character_set_filesystem | binary                         |
    | character_set_results    | utf8                           |
    | character_set_server     | utf8                           |
    | character_set_system     | utf8                           |
    | character_sets_dir       | D:\xampp\mysql\share\charsets\ |
    +--------------------------+--------------------------------+
    ```
- 查看当前数据表字符集

    ```sql
    mysql>  show create table tb_name;

    //输出
   +--------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | Table  | Create Table                                                                                                                                                                  |
    +--------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | testcn | CREATE TABLE `testcn` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `name` varchar(20) DEFAULT NULL,
    PRIMARY KEY (`id`)
    ) ENGINE=MyISAM AUTO_INCREMENT=3 DEFAULT CHARSET=utf8 |
    +--------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    ```

    > 一般来说, utf8 最为通用,所以字符集设置为 uft8

- 修改mysql配置文件 my.ini 中所有其他字符改为 utf8

- mysql控制台下运行以下命令

    ```sql
    mysql> set names utf8;
    mysql> ALTER DATABASE db_name DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
    mysql> ALTER TABLE tb_name DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
    ```

- 重启mysql 这时数据库的编码就是utf8了

## 简化命令版

> 如果上面的没有效果,可以用下面的命令

- 设置

    ```sql
    set character_set_client = utf8;
    set character_set_server = utf8;
    set character_set_connection = utf8;
    set character_set_database = utf8;
    set character_set_results = utf8;
    set collation_connection = utf8_general_ci;
    set collation_database = utf8_general_ci;
    set collation_server = utf8_general_ci;
    ```
- 检查

    ```sql
    mysql> show variables like "%char%";
    ```

- 重启(linux)

    ```sql
    /etc/init.d/mysqld restart
    ```
