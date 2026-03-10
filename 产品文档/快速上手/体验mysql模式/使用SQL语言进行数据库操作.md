本章节将介绍YashanDB数据库中相关的基本语法和示例，更多SQL语法介绍请参考[SQL参考手册](../../开发手册/SQL参考手册/00SQL参考手册)。

## 连接数据库

### 方案一：通过YashanDB客户端工具yasql工具连接数据库

[快速体验YashanDB](快速体验YashanDB)章节已完成服务器本地通过yasql工具连接数据库并创建数据库测试用户，yasql客户端工具还支持部署到远端的64位Linux服务器或Windows服务器上。以下以Windows部署数据库客户端工具为例介绍详细操作步骤。

1. 下载数据库客户端工具包yashandb-client-{版本号}-windows-amd64.zip到64位Window环境，并解压至本地路径。

   ```cmd
    > d:
      
   D:\> cd D:\yashandb-client
   D:\yashandb-client> dir
   2023/02/10  14:57    <DIR>          .
   2023/02/10  14:57    <DIR>          ..
   2023/02/10  14:55    <DIR>          bin
   2023/02/10  14:55    <DIR>          include
   2023/02/10  14:55    <DIR>          lib
   ```

2. 在Windows配置用户环境变量的工具中，为Path变量增加如下内容：

   ```cmd
   D:\yashandb-client\bin
   D:\yashandb-client\lib
   ```

3. 打开cmd窗口，以测试用户sales通过数据库客户端工具yasql连接数据库，数据库默认连接端口为1688。

   ```cmd
   > yasql sales/password@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {版本号} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
   
   SQL> 
   ```

YashanDB客户端工具还支持部署在Linux服务器上，请参考[YashanDB客户端安装](../../安装和升级/安装部署/YashanDB客户端安装/00YashanDB客户端安装)进行操作。

### 方案二：通过第三方工具连接数据库

崖山数据库23.4版本运行在mysql模式时，支持通过mysql-client5.7.42、Navicat11、Navicat12连接数据库服务进行SQL命令操作。本文以Navicat为例介绍数据库连接配置。

1. 下载安装Navicat开源软件以及MySQL的JDBC驱动5.1.40版本，由于其他版本驱动未完成完整兼容性测试，推荐您使用指定版本的驱动进行数据库连接。
2. 打开Navicat，使用指定驱动版本5.1.40连接数据库。其中：
    - 【用户名】及【密码】为[快速体验YashanDB](快速体验YashanDB)中创建的测试用户。
    - 【主机】和【端口号】为数据库安装路径下yashandb.toml文件中的mysql_addr的参数值，如按照默认配置进行安装，则主机为数据库服务器IP地址，端口号默认为1690。
3. （可选）当使用mysql-client5.7.42连接YashanDB服务时，连接串可参考`mysql -h ip_addr -P port -u user_name -p passwd`，**ip_addr**和**port**为数据库安装路径下yashandb.toml文件中的mysql_addr的参数值，如按照默认配置进行安装，则为数据库服务器IP地址，端口号默认为1690；**user_name**和**passwd**为[快速体验YashanDB](快速体验YashanDB)中创建的测试用户。

## SQL基本操作

1. 连接数据库后，创建本次测试使用的schema，并切换到该schema下。

   ```sql
   CREATE DATABASE example1;   
   USE example1;
   ```

2. 创建数据表，并插入数据。您可参考[样例表](../../开发手册/附：样例表.html#mysqlSqlExam)创建本次试用涉及的业务表。

   ```sql
   -- 创建普通无外键约束的业务表并插入数据
   CREATE TABLE area6
   (area_no CHAR(2) NOT NULL PRIMARY KEY,
    area_name VARCHAR(60),
    DHQ VARCHAR(20) DEFAULT 'ShenZhen' NOT NULL);

   INSERT INTO area6 VALUES ('01','华东','Shanghai');
   INSERT INTO area6 VALUES ('02','华西','Chengdu');
   INSERT INTO area6 VALUES ('03','华南','Guangzhou');
   INSERT INTO area6 VALUES ('04','华北','Beijing');
   INSERT INTO area6 VALUES ('05','华中','Wuhan');
   
   -- 创建含外键限制的业务表
   CREATE TABLE branches6 (
    branch_no CHAR(4) PRIMARY KEY,
    branch_name VARCHAR(200) NOT NULL,
    area_no CHAR(2),
    address VARCHAR(200),
    CONSTRAINT c_branches_1 FOREIGN KEY (area_no) REFERENCES area6(area_no) ON DELETE SET NULL
   );
   
   INSERT INTO branches6 VALUES ('0001','深圳','03','');
   INSERT INTO branches6 VALUES ('0101','上海','01','上海市静安区');
   INSERT INTO branches6 VALUES ('0102','南京','01','City of Nanjing');
   INSERT INTO branches6 VALUES ('0103','福州','01','');
   INSERT INTO branches6 VALUES ('0104','厦门','01','Xiamen');
   INSERT INTO branches6 VALUES ('0401','北京','04','');
   INSERT INTO branches6 VALUES ('0402','天津','04','');
   INSERT INTO branches6 VALUES ('0403','大连','04','大连市');
   INSERT INTO branches6 VALUES ('0404','沈阳','04','');
   INSERT INTO branches6 VALUES ('0201','成都','02','');
   INSERT INTO branches6 VALUES ('0501','武汉','05','');
   INSERT INTO branches6 VALUES ('0502','长沙','05','');
   
   -- 创建主键自增的业务表，并插入数据
   CREATE TABLE example(
    id INT(4) PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(25) NOT NULL
    );
   
   INSERT INTO example(name) VALUES('zhangsan');
   INSERT INTO example(name) VALUES('李四');
   INSERT INTO example(name) VALUES('tom');
   INSERT INTO example(name) VALUES('huamulan'),('gongqijun'),('kimi');
   
   -- 查询表数据，确认主键自增情况
   SELECT * FROM example;
   +----+-----------+
   | ID | NAME      |
   +----+-----------+
   |  1 | zhangsan  |
   |  2 | 李四      |
   |  3 | tom       |
   |  4 | huamulan      |
   |  5 | gongqijun      |
   |  6 | kimi    |
   +----+-----------+
   6 rows in set (0.00 sec)
   
   COMMIT;
   ```

3. 查看表中数据。

   ```sql
   --全表查询
   SELECT * FROM branches6;
   +-----------+-------------+---------+--------------------+
   | BRANCH_NO | BRANCH_NAME | AREA_NO | ADDRESS            |
   +-----------+-------------+---------+--------------------+
   | 0001      | 深圳        | 03      |                    |
   | 0101      | 上海        | 01      | 上海市静安区       |
   | 0102      | 南京        | 01      | City of Nanjing    |
   | 0103      | 福州        | 01      |                    |
   | 0104      | 厦门        | 01      | Xiamen             |
   | 0401      | 北京        | 04      |                    |
   | 0402      | 天津        | 04      |                    |
   | 0403      | 大连        | 04      | 大连市             |
   | 0404      | 沈阳        | 04      |                    |
   | 0201      | 成都        | 02      |                    |
   | 0501      | 武汉        | 05      |                    |
   | 0502      | 长沙        | 05      |                    |
   +-----------+-------------+---------+--------------------+
   12 rows in set (0.00 sec)

   -- 带CTE的分组排序查询。为了保证查询结果的数据一致性，YashanDB在执行group by分组、order by排序等查询时，要求group by、order by 的字段必须包含select查询的所有字段。

    WITH city_list(area_no,area_name,branch_no) AS (SELECT area6.area_no,area6.area_name, branches6.branch_no FROM area6, branches6 WHERE area6.area_no = branches6.area_no)
    SELECT city_list.area_no, city_list.area_name, COUNT(area_no) FROM city_list GROUP BY city_list.area_no, city_list.area_name ORDER BY COUNT(area_no);
   
   +---------+-----------+----------------+
   | area_no | area_name | COUNT(area_no) |
   +---------+-----------+----------------+
   | 02      | 华西      |              1 |
   | 03      | 华南      |              1 |
   | 05      | 华中      |              2 |
   | 01      | 华东      |              4 |
   | 04      | 华北      |              4 |
   +---------+-----------+----------------+
   5 rows in set (0.01 sec)
   
   ```

4. 创建表索引提高查询效率，查看索引创建情况：

   ```sql
   -- 创建索引
   CREATE INDEX idx_branches_1 ON branches6 (branch_name);
   Query OK, 0 rows affected (0.08 sec)
   
   -- 查询索引信息
   SHOW INDEX FROM branches6;
   +-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
   | Table     | Non_unique | Key_name       | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
   +-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
   | branches6 |          0 | PRIMARY        |            1 | BRANCH_NO   | A         |        NULL |     NULL | NULL   | YES  | BTREE      |         |               |
   | branches6 |          1 | idx_branches_1 |            1 | BRANCH_NAME | A         |        NULL |     NULL | NULL   |      | BTREE      |         |               |
   | branches6 |          1 | index_fk_75    |            1 | AREA_NO     | A         |        NULL |     NULL | NULL   | YES  | BTREE      |         |               |
   +-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
   3 rows in set (0.02 sec)
   
   -- 删除索引
   DROP INDEX idx_branches_1 ON branches6;
   Query OK, 0 rows affected (0.00 sec)
   
   ```

## 事务管理

在上面的示例步骤1中，我们使用了`commit`语句，该语句是YashanDB的事务控制语句。YashanDB事务管理是隐式启动的，由第一条可执行的SQL语句触发启动，通常当业务数据发生变更时将自动启动事务管理。提交事务前，用户在事务过程做的任何修改只有自己能看到，其他用户无法看到，并可以通过回滚操作将数据恢复。提交事务后，其他用户可看到修改后的数据，此时无法通过回滚操作将数据恢复。

YashanDB事务控制语句包括以下语句：

- COMMIT语句：

  用于提交当前事务，使事务所有的修改都持久化并生效。提交事务后，将归还事务所占用的所有资源，包括SAVEPOINT、锁资源、内存资源、UNDO。

- ROLLBACK语句：

  - ROLLBACK：用于回滚当前事务，使事务所有的修改被回退，丢弃更改。
  - ROLLBACK TO SAVEPOINT：只将事务数据和资源回退至SAVEPOINT处的状态，而不会结束整个事务。

- SAVEPOINT：

  用于标识一个保存点，记录了当前的事务状态和资源持有情况，后续事务可以回滚到已标记的保存点。

详细的事务管理请参考[事务管理](../../概念手册/事务机制/事务管理)。