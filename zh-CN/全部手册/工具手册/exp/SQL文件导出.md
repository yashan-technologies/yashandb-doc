exp工具支持使用--sql参数，导出数据库元数据到SQL文件。

## 命令格式

```shell
exp --sql username/password@ip:port [KEYWORD[=value1,value2,...,valueN]]
```

**username/password@ip:port**

执行导出命令的数据库用户（登录用户）密码、服务端地址，对其权限要求见不同导出模式具体描述。

**KEYWORD**

命令选项：

- FILE：导出元数据SQL文件的名称，对名称已存在的文件进行覆盖，必须输入。
- 导出模式如下，每次导出只能选择下列一种模式，若命令中未指定导出模式，则默认为FULL=N。
    - FULL：整库元数据SQL导出模式命令，Value为单值Y或N。
    - OWNER：用户元数据SQL导出模式命令，Value为用户名称。
    - TABLES：表元数据SQL导出模式命令，Value为表名称。
- ROWS：只支持导出元数据的SQL，即Value只能为单值N。可省略，效果与ROWS=N一致。
- LOG_PATH：用于指定导出日志的路径，Value为路径名，可省略，省略则不生成日志文件。
- LOG_LEVEL：用于指定导出日志的日志级别，Value为[ERROR, WARN, INFO, DEBUG, TRACE]，可省略，省略则默认为INFO。

示例

```shell
$ exp --sql sales/sales@127.0.0.1:1688 FILE=export.full.dump FULL=Y
```

## 命令选项

### FULL模式

FULL模式用于导出整库的元数据SQL，包括：

- 用户元数据SQL
- 用户下的所有对象元数据SQL
- 所有的系统权限元数据SQL
- 所有的角色元数据SQL
- 所有的审计策略/使能元数据SQL
- 所有的OUTLINE元数据SQL
- 所有的PROFILE元数据SQL
- 所有的SQLMAP元数据SQL
- 所有的定时任务元数据SQL

执行本模式导出的数据库用户必须拥有DBA角色权限。其中，Value值的含义为：

- Y：导出所有用户下的数据。
- N：导出登录用户下的数据。

示例

```shell
# 执行整库元数据SQL导出
$ exp --sql sales/sales FILE=export.full.dump FULL=Y
```

### OWNER模式

OWNER模式用于导出指定用户下的元数据SQL，包括：

- 用户下的所有对象元数据SQL

本模式命令的Value值为用户名称，可以为多个，输入重复用户名时将只导出一次。 最多可以输入1024个不同用户。

对于执行本模式导出的数据库用户，如Value值与此用户相同，即导出本用户下的数据，则对其权限无要求；如Value值中存在非本用户，即导出其他用户下的数据，则要求其必须拥有DBA角色权限。

示例

```shell
# 执行用户模式元数据SQL导出
$ exp --sql sales/sales FILE=export.owner.export OWNER=sales
```

### TABLES模式

TABLES模式用于导出指定的表元数据SQL，包括：

- 表的元数据SQL
- 基于表的索引、注释、约束、列属性等元数据SQL

本模式命令的Value值为表名称，可以为多个，输入重复表名时将只导出一次。最多可以输入1024个不同的表。

对于执行本模式导出的数据库用户，如其为导出表的OWNER，则对其无权限要求；否则，要求其拥有DBA角色权限。

示例

```shell 
# 执行表模式元数据SQL导出
$ exp --sql sales/sales FILE=export.table.export TABLES=sales_info
```

## 字符集设置

导出元数据SQL时，使用参数CHARACTER_SET，支持设置导出文件的字符集。

> **Note**:
>
> - 该参数取值范围为[ASCII,GBK,UTF8,ISO88591,GB18030]，影响导出文件的字符集。
> - 导出过程中命令返回信息的字符集由${YASDB_HOME}/client/yasc_env.ini中配置的客户端字符集参数CHARACTER_SET决定。
> - 导出文件名称的字符集与shell输入的字符集保持一致。
> - 请注意字符集的表示范围，如果设置的字符集不支持解析数据库中某些元数据符号（如GBK无法解析带表情包的表名），会导致导出数据失真乱码。

示例

```shell
$ exp --sql sales/sales@127.0.0.1:1688 FILE=%export_file% FULL=Y CHARACTER_SET=GBK
```
