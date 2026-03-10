在YashanDB（mysql模式）运行过程中，可能会因应用变化、资源扩充、性能调优等需要手动调整某个系统变量参数值。

## 查看系统变量

可通过以下方式查询系统变量相关信息：
- 通过MySQL客户端连接YashanDB（mysql模式）所创建的会话中执行SHQW GLOBAL|SESSION VARIABLES语句。

- 通过以下视图可以查看系统变量信息：

    - PERFORMANCE_SCHEMA.GLOBAL_VARIABLES：查询全局系统变量。

    - PERFORMANCE_SCHEMA.SESSION_VARIABLES：查询当前会话的系统变量。

## 修改系统变量值

### 参数说明

- 作用域：即系统变量的生效范围，分为全局（global）和会话（session），mysql模式下将在建立连接时用全局级变量初始化会话级变量，变量全局级的修改不会影响到已经建立的会话的变量会话级的值。

- 系统变量及其值域：param和value分别为配置参数名称和参数值，参数值需符合取值范围和格式，详情请查阅[系统变量（mysql模式）](../../参考手册/系统变量（mysql模式）)。

### 修改方式

修改系统变量的参数值的方式包括执行SET语句和编辑my.ini文件。

#### SET语句

通过MySQL客户端连接YashanDB（mysql模式）所创建的会话中执行SET语句和SET NAMES语句可以修改相应系统变量：

- SET语句：在线修改系统参数，但修改不会写入至my.ini文件（即数据库重启后配置将还原）。

- SET NAMES语句：设置客户端、连接与结果的字符集及连接的字符序。

- SET CHARSET语句：设置客户端与结果字符集。

- SET TRANSACTION语句：设置事务隔离级别。

示例（单机HEAP表）

```sql
SET @@SESSION.validate_password_length = 6;
```

#### my.ini文件

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

    VALIDATE_PASSWORD_LENGTH = 8
    ```
    
3. 重启数据库使配置生效。