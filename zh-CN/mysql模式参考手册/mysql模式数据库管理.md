mysql模式主要应用于业务用户权限管理、业务对象管理、业务数据读写等场景；在进行数据库运维管理操作时，需将对应的操作管理会话切换到yashan模式后进行操作。

## 需切换到yashan模式执行的数据库管理操作

-  备份恢复
-  闪回
-  实例启停
-  主备切换
-  数据同步
-  日志管理
-  文件管理
-  表空间及表空间集管理
-  表管理
-  资源管理
-  故障诊断
-  会话和计划管理

> **Note**:
>
> mysql模式下的备份恢复无备份集加密功能。

## 操作步骤

1. 以YashanDB原生配套的客户端及工具进行连接配置，此处以yasql工具为例，默认端口为1688。

    ```shell
    $ yasql username/password@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux

    SQL> 
    ```

2. 修改当前会话的语法模式。

    ```sql
    -- 查询当前会话的语法模式
    SHOW PARAMETER COMPAT_VECTOR;

    name                                                             value                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    COMPAT_VECTOR                                                    mysql    

    -- 切换到yashan模式，再执行相关运维操作
    ALTER SESSION SET COMPAT_VECTOR = yashan;

    SHOW PARAMETER COMPAT_VECTOR;

    name                                                             value                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    COMPAT_VECTOR                                                    yashan    
    ```

3. 切换到yashan模式后，可查看**数据库管理**章节了解详细的数据库管理步骤，操作完成后关闭会话或将会话切换到mysql模式再进行业务数据管理操作。

    ```sql
    -- 切换到mysql模式，再执行相关操作
    ALTER SESSION SET COMPAT_VECTOR = mysql;

    SHOW PARAMETER COMPAT_VECTOR

    name                                                             value                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    COMPAT_VECTOR                                                    mysql    
    ```