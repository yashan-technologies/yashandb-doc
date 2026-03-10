本章节将介绍YashanDB数据库中相关的基本语法和示例，更多SQL语法介绍请参考[SQL参考手册](../../开发手册/SQL参考手册/00SQL参考手册)。

## 连接数据库

### 方案一：通过客户端工具连接数据库

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
   > yasql sales/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {版本号} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
   
   SQL> 
   ```

YashanDB客户端工具还支持部署在Linux服务器上，请参考[YashanDB客户端安装](../../安装和升级/安装部署/YashanDB客户端安装/00YashanDB客户端安装)进行操作。

### 方案二：通过YDC工具连接数据库

崖山开发者中心（YashanDB Developer Center，YDC）是数据库配套的开发平台，用户可通过YDC可视化地管理数据库中的表、视图、函数等数据库对象。YDC支持Windows桌面版和Linux Web版安装，支持x86架构。本文以Windows桌面版YDC工具为例介绍安装使用操作步骤。

1. YDC工具依赖**WebView2 运行时**，请确认操作系统已下载并安装[WebView2 运行时](https://developer.microsoft.com/microsoft-edge/webview2#download-section)，选择对应架构的**Evergreen Standalone Installer**。
2. 登录[下载中心](https://download.yashandb.com/download)下载**yashandb-developer-center-xx.xx-amd64-installer.exe**桌面版YDC工具安装包。
3. 双击YDC工具安装包启动安装程序，根据提示完成客户端安装。
4. 启动YDC客户端工具，点击左侧数据源处的 **+** 号，使用[快速体验YashanDB](快速体验YashanDB)小节创建的数据库用户sales/sales测试数据库连接。

### 方案三：通过第三方管理工具连接数据库

除自带的yasql客户端工具外，YashanDB还支持对接DataGrip2021.1和DBeaver20.0.1等SQL开发工具。

本文以DBeaver为例展示YashanDB对接SQL开发工具的关键步骤：（DataGrip对接与DBeaver类似，详情请参考[DataGrip官方产品文档](https://www.jetbrains.com/help/datagrip/2021.1/meet-the-product.html?keymap=primary_windows)进行操作。

1. 下载安装DBeaver开源软件。
2. 打开DBeaver，为YashanDB新建数据库驱动器，并添加JDBC驱动包（从产品安装路径的drivers目录下可获取该jar包），也可以直接使用MySQL的驱动包连接YashanDB。
3. 创建到YashanDB的连接，url=`jdbc:yasdb://服务器IP:1688/sales`，其中：
    - sales为[快速体验YashanDB](快速体验YashanDB)中创建的测试用户。
    - 1688为数据库默认侦听端口，如[快速体验YashanDB](快速体验YashanDB)中修改了默认端口，请以实际配置的端口进行连接。
4. 测试连接，成功即表示与DBeaver对接成功。

## SQL基本操作

1. 创建数据表，并通过在数据表中插入业务数据，以下为操作示例：

   ```sql
   CREATE TABLE tb_yashan(c1 INT,C2 VARCHAR(10));
   
   INSERT INTO tb_yashan VALUES(4,'hello');
   
   INSERT INTO tb_yashan VALUES(1,'world'),(2,'nihao'),(3,'shijie');
   
   COMMIT;
   ```

2. 查看表中数据：

   ```sql
   SELECT c1,c2 FROM tb_yashan;
   
             C1 C2
   ------------ -------------
              1 nihao
              2 hello
              3 shijie
              4 world
   ```

3. 更新数据：

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

4. 创建表索引提高查询效率，并在`USER_INDEXES`视图中查看索引创建情况：

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

## 事务管理

在上面的示例步骤1中，我们使用了`commit`语句，该语句是YashanDB的事务控制语句。YashanDB事务管理是隐式启动的，由第一条可执行的SQL语句触发启动，通常当业务数据发生变更时将自动启动事务管理。提交事务前，用户在事务过程做的任何修改只有自己能看到，其他用户无法看到，并可以通过回滚操作将数据恢复。提交事务后，其他用户可看到修改后的数据，此时无法通过回滚操作将数据恢复。

YashanDB事务控制语句包括以下语句：

- COMMIT语句：

  用于提交当前事务，使事务所有的修改都持久化并生效。提交事务后，将归还事务所占用的所有资源，包括SAVEPOINT、锁资源、内存资源、UNDO。

- ROLLBACK语句：

  - ROLLBACK：用于回滚当前事务，使事务所有的修改被回退，丢弃更改。
  - ROLLBACK TO SAVEPOINT：只将事务数据和资源回退至SAVEPOINT处的状态，而不会结束整个事务。

- SAVEPOINT:

  用于标识一个保存点，记录了当前的事务状态和资源持有情况，后续事务可以回滚到已标记的保存点。

详细的事务管理请参考[事务管理](../../概念手册/事务机制/事务管理)。
