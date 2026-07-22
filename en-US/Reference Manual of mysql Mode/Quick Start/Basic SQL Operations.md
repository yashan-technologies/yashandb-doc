This chapter will introduce the basic syntax and examples related to the YashanDB database. For more SQL syntax information, please refer to the [SQL Reference Manual](../SQL Reference/00SQL Reference).

## Connecting to the Database

### Option 1: Connecting to the Database via YashanDB Client Tool *yasql*

The [Quick Start with YashanDB](Quick Start with YashanDB) chapter has completed connecting to the database through the *yasql* tool locally on the server and creating a test user for the database. The *yasql* client tool also supports deployment on remote 64-bit Linux servers or Windows servers. The following detailed steps will use Windows deployment of the database client tool as an example.

1. Download the database client toolkit **yashandb-client-{version_number}-windows-amd64.zip** to a 64-bit Windows environment and unzip it to a local path.

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

2. In the Windows user environment variable configuration tool, add the following content to the Path variable:

   ```cmd
   D:\yashandb-client\bin
   D:\yashandb-client\lib
   ```

3. Open the cmd window and connect to the database using the test user *sales* through the *yasql* client tool. The default connection port for the database is 1688.

   ```cmd
   > yasql \"sales\"/password@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> 
   ```

The YashanDB client tool also supports deployment on Linux servers. Please refer to [YashanDB Client Installation](../../Installation and Upgrade/Installation and Deployment/YashanDB Client Installation/00YashanDB Client Installation) for operational instructions.

### Option 2: Connecting to the Database via Third-Party Tools



When YashanDB version 23.4 runs in mysql mode, it supports connecting to the database service through mysql-client 5.7.42, Navicat 11, and Navicat 12 for SQL command operations. This article uses Navicat as an example to introduce the database connection configuration.

1. Download and install the software Navicat.
2. Open Navicat and use the MySQL driver to connect to the database. Among them:
    - The **username** and **password** are the test users created in the [Quick Start with YashanDB](Quick Start with YashanDB).
    - The **host** and **port number** are the parameter values of `mysql_addr` in the yashandb.toml file under the database installation path. If installed with default configuration, the host is the database server's IP address, and the port number defaults to 1690.
3. (Optional) When using mysql-client 5.7.42 to connect to YashanDB service, the connection string can refer to `mysql -h ip_addr -P port -u user_name -p passwd`, where **ip_addr** and **port** are the parameter values of `mysql_addr` in the yashandb.toml file under the database installation path. If installed with default configuration, it will be the database server's IP address and the port number defaults to 1690; **user_name** and **passwd** are the test users created in [Quick Start with YashanDB](Quick Start with YashanDB).
   ```sql
   $ mysql -h 127.0.0.1 -P 1690 -u sales -p
   Enter password:
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 0
   Server version: 5.7.42
   ```



## Basic SQL Operations

1. After connecting to the database, create the schema for this test and switch to that schema.

   ```sql
   CREATE DATABASE example1;   
   USE example1;
   ```

2. Create the data table and insert data. You can refer to the [Sample Tables](../../Development Guide/Appx2 Sample Tables.md#mysqlSqlExam) for creating the business tables involved in this trial.

   ```sql
   -- Create a regular business table without foreign key constraints and insert data
   CREATE TABLE area6
   (area_no CHAR(2) NOT NULL PRIMARY KEY,
    area_name VARCHAR(60),
    DHQ VARCHAR(20) DEFAULT 'ShenZhen' NOT NULL);

   INSERT INTO area6 VALUES ('01','EastChina','Shanghai');
   INSERT INTO area6 VALUES ('02','WestChina','Chengdu');
   INSERT INTO area6 VALUES ('03','SouthChina','Guangzhou');
   INSERT INTO area6 VALUES ('04','NorthChina','Beijing');
   INSERT INTO area6 VALUES ('05','CentralChina','Wuhan');
   
   -- Create a business table with foreign key constraints
   CREATE TABLE branches6 (
    branch_no CHAR(4) PRIMARY KEY,
    branch_name VARCHAR(200) NOT NULL,
    area_no CHAR(2),
    address VARCHAR(200),
    CONSTRAINT c_branches_1 FOREIGN KEY (area_no) REFERENCES area6(area_no) ON DELETE SET NULL
   );
   
   INSERT INTO branches6 VALUES ('0001','Shenzhen','03','');
   INSERT INTO branches6 VALUES ('0101','Shanghai','01','Jingan District, Shanghai');
   INSERT INTO branches6 VALUES ('0102','Nanjing','01','City of Nanjing');
   INSERT INTO branches6 VALUES ('0103','Fuzhou','01','');
   INSERT INTO branches6 VALUES ('0104','Xiamen','01','Xiamen');
   INSERT INTO branches6 VALUES ('0401','Beijing','04','');
   INSERT INTO branches6 VALUES ('0402','Tianjin','04','');
   INSERT INTO branches6 VALUES ('0403','Dalian','04','Dalian City');
   INSERT INTO branches6 VALUES ('0404','Shenyang','04','');
   INSERT INTO branches6 VALUES ('0201','Chengdu','02','');
   INSERT INTO branches6 VALUES ('0501','Wuhan','05','');
   INSERT INTO branches6 VALUES ('0502','Changsha','05','');
   
   -- Create a business table with auto-incrementing primary key and insert data
   CREATE TABLE example(
    id INT(4) PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(25) NOT NULL
    );
   
   INSERT INTO example(name) VALUES('zhangsan');
   INSERT INTO example(name) VALUES('Lisi');
   INSERT INTO example(name) VALUES('tom');
   INSERT INTO example(name) VALUES('huamulan'),('gongqijun'),('kimi');
   
   -- Query table data to confirm primary key auto-increment status
   SELECT * FROM example;
   +----+-----------+
   | id | name      |
   +----+-----------+
   |  1 | zhangsan  |
   |  2 | Lisi      |
   |  3 | tom       |
   |  4 | huamulan      |
   |  5 | gongqijun      |
   |  6 | kimi    |
   +----+-----------+
   6 rows in set (0.00 sec)
   
   commit;
   ```

3. View the data in the table.

   ```sql
   -- Query all table data
   select * from branches6;
   +-----------+-------------+---------+--------------------+
   | branch_no | branch_name | area_no | address            |
   +-----------+-------------+---------+--------------------+
   | 0001      | Shenzhen        | 03      |                    |
   | 0101      | Shanghai        | 01      | Jingan District, Shanghai       |
   | 0102      | Nanjing        | 01      | City of Nanjing    |
   | 0103      | Fuzhou        | 01      |                    |
   | 0104      | Xiamen        | 01      | Xiamen             |
   | 0401      | Beijing        | 04      |                    |
   | 0402      | Tianjin        | 04      |                    |
   | 0403      | Dalian        | 04      | Dalian City             |
   | 0404      | Shenyang        | 04      |                    |
   | 0201      | Chengdu        | 02      |                    |
   | 0501      | Wuhan        | 05      |                    |
   | 0502      | Changsha        | 05      |                    |
   +-----------+-------------+---------+--------------------+
   12 rows in set (0.00 sec)

   -- Group and sort query with CTE. To ensure data consistency in query results, YashanDB requires that columns in GROUP BY and ORDER BY must include all columns of the SELECT query.

    WITH city_list(area_no,area_name,branch_no) AS (SELECT area6.area_no,area6.area_name, branches6.branch_no FROM area6, branches6 WHERE area6.area_no = branches6.area_no)
    SELECT city_list.area_no, city_list.area_name, COUNT(area_no) from city_list GROUP BY city_list.area_no, city_list.area_name ORDER BY COUNT(area_no);
   
   +---------+-----------+----------------+
   | area_no | area_name | COUNT(area_no) |
   +---------+-----------+----------------+
   | 02      | WestChina      |              1 |
   | 03      | SouthChina      |              1 |
   | 05      | CentralChina      |              2 |
   | 01      | EastChina      |              4 |
   | 04      | NorthChina      |              4 |
   +---------+-----------+----------------+
   5 rows in set (0.01 sec)
   
   ```

4. Create table indexes to improve query efficiency and check the index creation status:

   ```sql
   -- Create index
   CREATE INDEX idx_branches_1 ON branches6 (branch_name);
   Query OK, 0 rows affected (0.08 sec)
   
   -- Query index information
   SHOW INDEX FROM branches6;
   +-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
   | Table     | Non_unique | Key_name       | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
   +-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
   | branches6 |          0 | PRIMARY        |            1 | BRANCH_NO   | A         |        NULL |     NULL | NULL   | YES  | BTREE      |         |               |
   | branches6 |          1 | idx_branches_1 |            1 | BRANCH_NAME | A         |        NULL |     NULL | NULL   |      | BTREE      |         |               |
   | branches6 |          1 | index_fk_75    |            1 | AREA_NO     | A         |        NULL |     NULL | NULL   | YES  | BTREE      |         |               |
   +-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
   3 rows in set (0.02 sec)
   
   -- Delete index
   DROP INDEX idx_branches_1 ON branches6;
   Query OK, 0 rows affected (0.00 sec)
   
   ```

## Transaction Management

In the first step of the example above, we used the `commit` statement, which is the transaction control statement of YashanDB. YashanDB transaction management is implicitly started, triggered by the first executable SQL statement, usually when business data changes. Before committing a transaction, any modifications made by the user during the transaction can only be seen by themselves, and other users cannot see them. The data can be restored through rollback operations. After committing the transaction, other users can see the modified data, and at this point, the data cannot be restored through rollback operations.

YashanDB transaction control statements include the following commands:

- **COMMIT statement**:

  Used to commit the current transaction, making all modifications of the transaction persistent and effective. After committing the transaction, all resources occupied by the transaction will be released, including SAVEPOINT, lock resources, memory resources, and UNDO.

- **ROLLBACK statement**:

  - **ROLLBACK**: Used to roll back the current transaction, undoing all modifications of the transaction.
  - **ROLLBACK TO SAVEPOINT**: Only rolls back the transaction data and resources to the state at the SAVEPOINT without ending the entire transaction.

- SAVEPOINT:

  Used to mark a save point that records the current transaction status and resource holding situation, allowing subsequent transactions to roll back to the marked save point.

For detailed transaction management, please refer to [Transaction Management](../../Product Concepts/Transaction/Transaction Management).
