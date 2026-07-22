yaspwd工具用于维护数据库中系统用户（如SYS）的密码和导出UKEY的公钥文件。该类用户的安全等级较高，请谨慎操作本工具。

前提条件
----

本工具依赖于libyas\_infra.so和libcrypto.so，导出UKEY公钥则额外需要ukey\_gm3000.so，请先确认如下环境变量已配置。

```shell
$ export LD_LIBRARY_PATH=${YASDB_HOME}/lib:${LD_LIBRARY_PATH}
```

命令格式
----

使用本工具的命令格式如下：

```shell
yaspwd file=<fname> password=<password>
input_file=<input-fname>
'sys={y | password}'
'sysbackup={y | password}'
'sysdg={y | password}'
'syskm={y | password}'
```

其中：

- '='前后不应有空格，否则命令无法执行。
- 多次执行相同命令时，后续输入密码将覆盖之前的密码。
- SYS用户的密码为必输项，即执行本命令必须为其指定密码，其他系统用户（SYSBACKUP/SYSDG/SYSKM）则可选。

> **Note**: 
>
> 目前YashanDB的系统用户只有SYS，仅需要指定SYS用户的密码。

**fname**

密码文件名称，可带路径指定，未指定路径时，在当前目录下生成文件。不可指定为已存在的文件，否则报错。

> **Caution**: 
>
> 本工具支持将密码文件指定到任意位置，均可以成功生成，但该密码文件是否生效取决于YashanDB的配置参数PASSWORD_FILE，该参数用于定义系统用户的密码获取路径及文件名称，初始值默认为$YASDB\_DATA/instance/yasdb.pwd。
>
> 用户只有在将密码文件按照PASSWORD_FILE所指定的路径及名称存放，文件中的密码才会生效。

**password**

由用户输入的字符串所组成的密码内容，该字符串需遵循[密码强度](../产品安全/身份标识与鉴别/密码认证/密码策略.md#password_complexity)要求。

**input-fname**

密码文件名称，该文件必须为已存在的文件，用于从该文件内容生成新的密码文件。

**y**

指定此选项时，将提示用户为其对应的用户输入新密码。

生成密码
----

在数据库完成安装后，可通过如下操作步骤为系统用户生成新密码：

1. 以数据库安装用户登录数据库服务器，进入配置参数PASSWORD_FILE指定的路径（默认为$YASDB\_DATA/instance/）。

2. 查看路径下是否已存在yasdb.pwd文件，如已存在，将其更名。

    ```shell
    $ mv yasdb.pwd yasdb1.pwd
    ```

3. 通过yaspwd命令生成密码，其中SYS用户密码为默认和必须的指定项。

    ```shell
    # 方式一：仅指定密码文件，根据提示输入SYS用户的密码
    $ yaspwd file=yasdb.pwd
    Enter password for SYS:

    # 方式二：同时指定密码文件和SYS用户密码
    $ yaspwd file=yasdb.pwd password=yasdb_123

    # 方式三：同时指定其他系统用户密码
    $ yaspwd file=yasdb.pwd sysbackup=y
    $ yaspwd file=yasdb.pwd password=yasdb_123 sysbackup=yasdb_1234

    # 方式四：通过其他密码文件生成新密码文件，此时SYS用户密码也被复制
    $ yaspwd file=yasdb.pwd input_file=yasdb_input.pwd

    # 方式五：通过其他密码文件生成新密码文件，并重新指定SYS密码（不可指定其他系统用户密码）
    $ yaspwd file=yasdb.pwd input_file=yasdb_input.pwd sys=y
    Enter password for SYS:
    ```

4. 使用新密码即可成功登录YashanDB。

修改密码
----

在数据库运行过程中，可随时通过yaspwd修改系统用户密码。

> **Warn**:
>
> 密码为数据库用户的最基本鉴权，请确保在不对系统运行造成影响的情况下，执行修改密码操作。

本工具仅能修改单个节点的系统用户密码，如需修改数据库集群中所有节点的系统用户密码见[yasboot](yasboot/yasboot命令介绍/yasboot cluster)章节描述。

用户可通过如下操作步骤为系统用户指定新密码：

1. 以数据库安装用户登录数据库服务器。

2. 通过yaspwd命令修改密码。

    ```shell
    # 方式一：仅指定密码文件，根据提示输入SYS用户的密码
    $ yaspwd file=yasdb_new.pwd
    Enter password for SYS:

    # 方式二：同时指定密码文件和SYS用户密码，若密码中含有OS命令相关的特殊字符（例如@、/、.、!、$等）需进行转义
    $ yaspwd file=yasdb_new.pwd password=yasdb_123

    # 方式三：同时指定其他系统用户密码，若密码中含有OS命令相关的特殊字符（例如@、/、.、!、$等）需进行转义
    $ yaspwd file=yasdb_new.pwd sysbackup=y
    $ yaspwd file=yasdb_new.pwd password=yasdb_123 sysbackup=yasdb_1234

    # 方式四：通过其他密码文件生成新密码文件，此时SYS用户密码也被复制
    $ yaspwd file=yasdb_new.pwd input_file=yasdb_input.pwd

    # 方式五：通过其他密码文件生成新密码文件，并重新指定SYS密码（不可指定其他系统用户密码）
    $ yaspwd file=yasdb_new.pwd input_file=yasdb_input.pwd sys=y
    Enter password for SYS:
    ```

3. 使用新密码登录YashanDB，当登录失败时，继续执行下述步骤。

4. 使用旧密码登录YashanDB，查看PASSWORD_FILE参数值。

    ```sql
    SHOW PARAMETER PASSWORD_FILE;
    NAME                VALUE                                                            
    ------------------- -------------------------------------------------
    PASSWORD_FILE       ?/instance/a.pwd    
    ```

5. 修改PASSWORD_FILE参数值为新密码文件（重启生效），或者将新密码文件更名为PASSWORD_FILE参数值。

    ```sql
    -- 修改PASSWORD_FILE参数示例，其中参数值请修改为实际新密码文件路径及名称
    ALTER SYSTEM SET PASSWORD_FILE=?/instance/yasdb_new.pwd SCOPE=SPFILE;
    -- 上述命令成功后，重启数据库
    ```

6. 使用新密码即可成功登录YashanDB。

UKEY导出公钥
----

在使用UKEY认证登录前，需初始化UKEY并导出公钥。

> **Caution**: 
>
> UKEY的初始化和导出过程中，设备上只能有一个UKEY。

用户可通过如下操作步骤导出UKEY公钥：

1. 设备上插入UKEY，确保只有一个UKEY。

2. 通过yaspwd命令指定用户角色，初始化UKEY，导出对应UKEY公钥。
    
    用户角色只能为DBA、SECURITY_ADMIN或AUDIT_ADMIN中，不能选错。

    DBA角色：
    ```shell
    $ yaspwd ukey_role=DBA
    ```
    安全员角色：
    ```shell
    $ yaspwd ukey_role=SECURITY_ADMIN
    ```
    审计员角色：
    ```shell
    $ yaspwd ukey_role=AUDIT_ADMIN
    ```

3. 如果设备没有初始化，则需要按照提示数据设备认证码、admin密码、用户密码进行初始化，如果设备已经初始化，则需要输入用户密码进行UKEY认证。

4. 如果设备已经存在密钥对，则按照提示可以重新生成新的密钥对或不需要重新生成密钥对。

5. 按照提示会将公钥文件导出到当前目录下，导出成功。
