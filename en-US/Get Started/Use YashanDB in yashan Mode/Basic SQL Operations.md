This chapter will introduce the basic syntax and examples related to the YashanDB database. For more SQL syntax information, please refer to the [SQL Reference Manual](../../All Manuals/Development Guide/SQL Reference Manual/00SQL Reference Manual).

## Connecting to the Database

### Option 1: Connect to the Database via Client Tools

The [Quick Start with YashanDB](Quick Start with YashanDB) chapter has completed connecting to the database and creating a test user through the *yasql* tool on the server locally. The *yasql* client tool also supports deployment on remote 64-bit Linux servers or Windows servers. The following detailed steps will illustrate the process using Windows to deploy the database client tool.

1. Download the database client tool package **yashandb-client-{version_number}-windows-amd64.zip** to a 64-bit Windows environment and extract it to a local path.

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

2. In the Windows user environment variable configuration tool, add the following content to the **Path** variable:

   ```cmd
   D:\yashandb-client\bin
   D:\yashandb-client\lib
   ```

3. Open the cmd window and connect to the database using the *yasql* client tool with the test user sales. The default connection port for the database is 1688.

   ```cmd
   > yasql sales/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> 
   ```

The YashanDB client tool also supports deployment on Linux servers. Please refer to [YashanDB Client Installation](../../All Manuals/Installation and Upgrade/Installation and Deployment/YashanDB Client Installation/00YashanDB Client Installation) for further steps.

### Option 2: Connect to the Database via YDC Tool

The YashanDB Developer Center (YDC) is a complementary development platform for the database, where users can visually manage database objects such as tables, views, and functions. YDC supports installation on Windows desktop and Linux web version, with support for x86 architecture. This article will demonstrate the installation and usage steps using the Windows desktop version of the YDC tool.

1. The YDC tool depends on **WebView2 Runtime**. Please ensure that the operating system has downloaded and installed the [WebView2 Runtime](https://developer.microsoft.com/microsoft-edge/webview2#download-section), selecting the corresponding architecture's **Evergreen Standalone Installer**.
2. Log in to the [Download Center](https://download.yashandb.com/download) to download the **yashandb-developer-center-xx.xx-amd64-installer.exe** desktop version YDC tool installation package.
3. Double-click the YDC tool installation package to start the installation process and complete the client installation according to the prompts.
4. Start the YDC client tool, click the **+** sign in the left data source section, and use the database user sales/sales created in the [Quick Start with YashanDB](Quick Start with YashanDB) section to test the database connection.

### Option 3: Connect to the Database via Third-party Management Tools

In addition to the built-in *yasql* client tool, YashanDB also supports integration with SQL development tools such as DataGrip 2021.1 and DBeaver 20.0.1.

This article demonstrates the key steps of integrating YashanDB with SQL development tools using DBeaver as an example: (Integration with DataGrip is similar to DBeaver. For details, please refer to the [DataGrip Official Product Documentation](https://www.jetbrains.com/help/datagrip/2021.1/meet-the-product.html?keymap=primary_windows)).

1. Download and install the DBeaver open-source software.
2. Open DBeaver, create a new database driver for YashanDB, and add the JDBC driver package (this jar package can be obtained from the drivers directory in the product installation path). You can also directly use the MySQL driver package to connect to YashanDB.
3. Create a connection to YashanDB, with the URL=`jdbc:yasdb://server_ip:1688/sales`, where:
    - sales is the test user created in the [Quick Start with YashanDB](Quick Start with YashanDB) section.
    - 1688 is the default listening port of the database. If you changed the default port as mentioned in the [Quick Start with YashanDB](Quick Start with YashanDB), please connect using the actual configured port.
4. Test the connection. A successful test indicates successful integration with DBeaver.

## Basic SQL Operations

1. Create a data table and insert business data into the table. Below are operational examples:

   ```sql
   CREATE TABLE tb_yashan(c1 INT,C2 VARCHAR(10));
   
   INSERT INTO tb_yashan VALUES(4,'hello');
   
   INSERT INTO tb_yashan VALUES(1,'world'),(2,'nihao'),(3,'shijie');
   
   COMMIT;
   ```

2. View the data in the table:

   ```sql
   SELECT c1,c2 FROM tb_yashan;
   
             C1 C2
   ------------ -------------
              1 nihao
              2 hello
              3 shijie
              4 world
   ```

3. Update data:

   ```sql
   UPDATE tb_yashan SET c1=5 WHERE c1=1;
   
   COMMIT;
   
   SELECT c1,c2 FROM tb_yashan;
   
             C1 C2
   ------------ -------------
              5 nihao
              2 hello
              3 shijie
              4 world
   ```

4. Create a table index to improve query efficiency and check index creation status in the `USER_INDEXES` view:

   ```sql
   CREATE INDEX inde1 ON tb_yashan(c1);
   
   SELECT * FROM USER_INDEXES;
   
   INDEX_NAME     INDEX_TYPE     TABLE_OWNER     TABLE_NAME      TABLE_TYPE       UNIQUENESS COMPRESSION PREFIX_LENGTH TABLESPACE_NAME     INI_TRANS    MAX_TRANS        PCT_FREE LOGGING       BLEVEL           LEAF_BLOCKS         DISTINCT_KEYS AVG_LEAF_BLOCKS_PER_KEY AVG_DATA_BLOCKS_PER_KEY STATUS                 NUM_ROWS           SAMPLE_SIZE LAST_ANALYZED         PARTITIONED TEMPORARY GENERATED VISIBILITY    DATABASE_MAINTAINED CONSTRAINT_INDEX
   -------------  -------------- --------------- --------------- ---------------- ---------- ----------- ------------- ------------------- ------------ ------------ ------------ ------- ------------ --------------------- --------------------- ----------------------- ----------------------- --------- --------------------- --------------------- --------------------- ----------- --------- --------- ------------- ------------------- ----------------
   INDE1          NORMAL         YASHAN          TB_YASHAN        TABLE            N          DISABLED                0 USERS                          2          255            8 Y                                                                                                                VALID                                                                       N           N         N         VISIBLE       N                   N
   
   SELECT INDEX_NAME FROM USER_INDEXES;
   
   INDEX_NAME
   -------------
   INDE1
   ```

## Transaction Management

In the example step 1 above, we used the `commit` statement, which is a transaction control statement in YashanDB. YashanDB's transaction management is implicitly initiated, triggered by the first executable SQL statement, usually when business data changes, and the transaction management will automatically start. Any modifications made by the user during the transaction are only visible to themselves until the transaction is committed, while other users cannot see them, and the data can be restored through rollback operations. After committing the transaction, other users can see the modified data, and it cannot be restored through rollback operations.

YashanDB transaction control statements include the following:

- COMMIT statement:

  It is used to commit the current transaction, making all modifications of the transaction persistent and effective. After committing the transaction, all resources occupied by the transaction will be released, including SAVEPOINT, locked resources, memory resources, and UNDO.

- ROLLBACK statement:

  - ROLLBACK: It is used to roll back the current transaction, discarding any changes made during the transaction.
  - ROLLBACK TO SAVEPOINT: It rolls back the transaction data and resources to the status at the SAVEPOINT without ending the entire transaction.

- SAVEPOINT:

  It is used to mark a save point, recording the current transaction status and resource holding condition. Subsequent transactions can roll back to the marked save point.

For detailed transaction management, please refer to [Transaction Management](../../All Manuals/Product Concepts/Transaction/Transaction Management).