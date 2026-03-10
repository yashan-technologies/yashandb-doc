YashanDB MySQL兼容模式支持通过系统变量SQL_MODE控制一些SQL语法和行为的限制和规范，不同的SQL模式对于数据库行为有很大影响。

## 配置参数说明

SQL_MODE系统变量的配置为多项组合型配置，可配置范围及对应含义如下表所示。

|选项值|参数说明|是否默认配置|
|----|----|----|
|ALLOW_INVALID_DATES         |仅完成语法兼容，无实际含义      |否      |
|ANSI_QUOTES                 |启用了ANSI_QUOTES配置项后，双引号和反引号都将被解释为标识符引用符号，单引号用于字符串引用符号；未启用ANSI_QUOTES选项时，双引号将被解释为字符串引用符号，反引号被解释为标识符引用符号。    |      否      |
|ERROR_FOR_DIVISION_BY_ZERO  |仅完成语法兼容，无实际含义      |是      |
|HIGH_NOT_PRECEDENCE         |仅完成语法兼容，无实际含义      |否      |
|IGNORE_SPACE                |仅完成语法兼容，无实际含义      |是      |
|NO_AUTO_CREATE_USER         |仅完成语法兼容，无实际含义      |是      |
|NO_AUTO_VALUE_ON_ZERO       |该选项用于控制AUTO_INCREMENT列处理的模式。配置该选项后，写入0到AUTO_INCREMENT列不会触发自增行为，只有插入NULL才会生成下一个序列号。反之，向AUTO_INCREMENT列插入NULL或0时都将自动生成下一个序列号。 |是      |
|NO_BACKSLASH_ESCAPES        |该配置项用于控制是否将反斜杠字符`\`作为字符串和标识符中的转义字符。在启用此模式后，反斜杠将变成像其他字符一样的普通字符，并且LIKE表达式的默认转义序列也将更改，不再使用转义字符。  |是      |
|NO_DIR_IN_CREATE            |仅完成语法兼容，无实际含义。     |否      |
|NO_ENGINE_SUBSTITUTION      |仅完成语法兼容，无实际含义。 |是      |
|NO_UNSIGNED_SUBTRACTION     |仅完成语法兼容，无实际含义      |是      |
|NO_ZERO_DATE                |该选项用于控制是否允许将'0000-00-00'作为有效日期进行插入，配置该选项时表示不允许。 |是      |
|NO_ZERO_IN_DATE             |该选项用于控制日期中月份或日份为0的情况是否可以被写入数据库，配置该选项时表示不允许。       |是      |
|ONLY_FULL_GROUP_BY          |仅完成语法兼容，无实际含义  |是      |
|PAD_CHAR_TO_FULL_LENGTH     |该选项用于控制在检索CHAR列值时是否保留尾部的空格，并将CHAR值填充到其实际完整长度。该选项仅对CHAR列生效，对VARCHAR项的尾部空格不做控制。  |否       |
|PIPES_AS_CONCAT             |通常`||`是数据库保留的逻辑运算符OR，当启用该选项后，`||`将被解释为字符串连接运算符，类似于CONCAT()函数的功能。  |否     |
|REAL_AS_FLOAT               |配置该选项将控制把REAL视为FLOAT的同义词，反之REAL将作为DOUBLE进行控制。      |否       |
|STRICT_ALL_TABLES           |仅完成语法兼容，无实际含义   |是      |
|STRICT_TRANS_TABLES         |仅完成语法兼容，无实际含义     |是      |
|TIME_TRUNCATE_FRACTIONAL    |仅完成语法兼容，无实际含义      |是     |

## 生效范围

该系统变量支持下列三种生效范围：

- 会话级生效：配置后在当前会话生效；
- 全局生效：配置后在所有新建会话生效，但重启后失效；
- 重启后全局生效：配置后需要重启才会在所有会话生效；

## 配置示例

配置mysql模式的系统变量时，需要通过MySQL客户端或第三方工具进行配置，本章节以mysql_client工具进行操作演示。

::: tabs

== 修改会话级SQL_MODE

```sql
-- 在当前会话查看全局和会话级的SQL_MODE配置
mysql> select @@global.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@global.sql_mode                                                                                                                                                                                                                               |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> select @@session.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@session.sql_mode                                                                                                                                                                                                                              |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

-- 修改当前会话的SQL_MODE，确认仅在当前会话生效，全局配置未生效
mysql> set @@session.sql_mode='STRICT_TRANS_TABLES';
Query OK, 0 rows affected (0.00 sec)

mysql> select @@global.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@global.sql_mode                                                                                                                                                                                                                               |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> select @@session.sql_mode;
+---------------------+
| @@session.sql_mode  |
+---------------------+
| STRICT_TRANS_TABLES |
+---------------------+

-- 需要追加SQL_MODE配置项时，可通过匿名块快速配置，以下为增加配置PIPES_AS_CONCAT为例

mysql> SET @@SESSION.sql_mode = CONCAT(@@SESSION.sql_mode, ",PIPES_AS_CONCAT");
Query OK, 0 rows affected (0.00 sec)

```

== 修改全局SQL_MODE

修改全局SQL_MODE需要以拥有DBA权限的用户进行操作（sys用户除外），配置结果将在新建会话生效，重启数据库实例后失效。

```sql
-- 查看全局SQL_MODE和会话级SQL_MODE配置
mysql> select @@global.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@global.sql_mode                                                                                                                                                                                                                               |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> select @@session.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@session.sql_mode                                                                                                                                                                                                                              |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.01 sec)

-- 修改全局SQL_MODE
mysql> set @@global.sql_mode='ANSI_QUOTES';
Query OK, 0 rows affected (0.00 sec)

mysql> select @@global.sql_mode;
+-------------------+
| @@global.sql_mode |
+-------------------+
| ANSI_QUOTES       |
+-------------------+
1 row in set (0.00 sec)

-- 当前会话未生效
mysql> select @@session.sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| @@session.sql_mode                                                                                                                                                                                                                              |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

-- 新建会话连接数据库，查询变量发现新会话中的系统变量已生效
[yashan@host2 ~]$ mysql -h 172.16.60.92 -P 4690 -u username -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 0
Server version: 5.7.42

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> select @@session.sql_mode;
+--------------------+
| @@session.sql_mode |
+--------------------+
| ANSI_QUOTES        |
+--------------------+
1 row in set (0.01 sec)

mysql> select @@global.sql_mode;
+-------------------+
| @@global.sql_mode |
+-------------------+
| ANSI_QUOTES       |
+-------------------+
1 row in set (0.01 sec)

-- 需要追加SQL_MODE配置项时，可通过匿名块快速配置，以下为增加配置PIPES_AS_CONCAT为例

mysql> SET @@global.sql_mode = CONCAT(@@global.sql_mode, ",PIPES_AS_CONCAT");
Query OK, 0 rows affected (0.00 sec)

```

== 修改文件级SQL_MODE



通过编辑my.ini文件修改系统参数需要重启数据库才能生效，且其作用域固定为全局。YashanDB安装后my.ini文件不会自动生成，需手动在$YASDB_DATA/config路径下创建。

1. 查询$YASDB_DATA/config路径下是否存在my.ini文件。

    ```shell
    $ echo $YASDB_DATA
    /data/yashan/yasdb_data/db-1-1 # 本文以/data/yashan/yasdb_data/db-1-1为例

    $ cd /data/yashan/yasdb_data/db-1-1/config 
    $ ll
    ```

2. 创建/编辑my.ini文件，保存并退出。



    ```shell
    $ vi my.ini
    # 在文件中插入需要修改的系统变量键值对，或修改已有键值对的参数值

    SQL_MODE = ANSI_QUOTES,ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_BACKSLASH_ESCAPES,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL
    ```
    
3. 以数据库安装用户通过yasboot命令重启数据库使配置生效。

    ```shell
    cd $HOME/install/bin
    yasboot cluster restart -c database_name
    +-------------------------------------------------------------------------------------------------------------+
    | type | uuid             | name                | hostid | index    | status  | return_code | progress | cost |
    +-------------------------------------------------------------------------------------------------------------+
    | task | a4893048b322c2a9 | ReStartYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 8    |
    +------+------------------+---------------------+--------+----------+---------+-------------+----------+------+
    task completed, status: SUCCESS
    ```

4. 通过msyql_client新建数据库连接，查看SQL_MODE参数值。

    ```sql
    mysql> select @@global.sql_mode;
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | @@global.sql_mode                                                                                                                                                                                                                                                                |
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ANSI_QUOTES,ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_BACKSLASH_ESCAPES,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    1 row in set (0.00 sec)

    mysql> select @@session.sql_mode;
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | @@session.sql_mode                                                                                                                                                                                                                                                               |
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ANSI_QUOTES,ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_BACKSLASH_ESCAPES,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL |
    +----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    1 row in set (0.00 sec)

    ```
:::
