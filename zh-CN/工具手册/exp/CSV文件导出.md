exp工具支持通过两种交互方式导出CSV数据，分别为命令行方式和配置文件方式。

## 使用命令行导出数据

### 命令格式

```shell
exp --csv {Common Options} {Export Options} {CSV Options}
```

### 命令选项-Common Options

具体选项如下表所示，[]表示可选项。

|  选项| 含义|
|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-f, --format*            | 数据文件格式，csv                                                                                                                                                                     |
| *-u, --user*              | 数据库用户名， 系统用户或普通用户，普通用户需要具备访问系统表权限                                                                                                                                              |
| *-p, --password*          | 数据库用户密码                                                                                                                                                                        |
| *[-L, --logfile]*         | 日志文件路径，缺省时直接将日志打印在前端，支持相对路径（不可为../相对路径）                                                                                                                                        |
| *[-F, --file]*            | csv文件导出路径，缺省时导出至当前路径，支持相对路径（不可为../相对路径）                                                                                                                                        |
| *[--lob]*                 | 设置LOB数据的导出方式，可选值lls/csv，默认值lls：<br>lls表示将LOB数据另外导出至一个单独的文件，当前csv文件中记录该单独文件名称，文件名称格式见[LOAD DATA](../../开发手册/SQL参考手册/SQL语句/LOAD DATA)中lls_column_clause描述<br>csv表示将lob导出到当前csv文件 |
| *[--inline-blob-format]*  | 当--lob为csv时，设置BLOB数据的导出方式，可选值binary/string，默认值binary：<br/>binary表示blob以二进制格式存储<br/>string表示blob以字符串格式存储                                                                        |
| *[--loglevel]*            | 日志等级，off/error/warn/info/debug/trace，默认值info                                                                                                                                   |
| *[--server-host]*         | 数据库IP及监听端口，默认值"127.0.0.1:1688"                                                                                                                                                 |
| *[--use-threads]*         | 工作线程数，默认值1                                                                                                                                                                     |
| *[-q, --query]*           | 设置查询语句导出对应结果集                                                                                                                                                                  |
| *[-qf, --query-file]*     | 指定sql文件，将文件中的查询语句导出对应结果集                                                                                                                                                       |
| *[-qo, --query-out-file]* | 设置查询语句导出对应结果集所在的文件名，与-F参数指定路径配合使用，不配置时，导出文件名为outfile                                                                                                                           |
| *[--bit-format]*          | 设置BIT数据的导出方式，可选值binary/decimal，默认值binary：<br/>binary表示bit列数据以二进制字符串形式导出存储<br/>decimal表示bit列数据以十进制字符串形式导出存储                                                                     |
| *[-ch, --character-set]*  | 设置导出文件的字符集，取值范围为[ASCII,GBK,UTF8,ISO88591,GB18030]，默认与客户端字符集相同                                                                                                                  |

> **Note**: 
>
> - 导出失败时，会删除指定文件名的导出文件，建议每次导出时指定不同的导出文件名，避免误删已成功导出的同名文件。
> - 命令行参数应小于16KB。

#### 参数说明

##### query-file参数

- --query或--query-file参数与--table、--owner参数互斥，使用时只能选择导出表或导出相应结果集中的一种模式。
- --query和--query-file参数可指定相应表名的schema，不指定时，默认为-u登录用户的schema。
- --query和--query-file参数只支持单条sql参数的导出，--query不支持换行、注释，--query-file支持在sql文件中换行、注释。
- --query和--query-file同时指定时，以--query为准。
- --query或--query-file参数生效时，--use-threads默认置为1，无法多线程导出。

##### character-set参数

- -ch参数影响导出csv的字符集以及lob文件的字符集。
- 导出过程中命令的返回信息以及导出后的日志文件的字符集由${YASDB_HOME}/client/yasc_env.ini中配置的客户端字符集参数CHARACTER_SET决定。
- 导出csv时，-qo与-T参数指定的导出文件名称的字符集与shell输入字符集保持一致。
- 导出sql结果集的csv时，指定的-qf参数的字符集由${YASDB_HOME}/client/yasc_env.ini中配置的客户端字符集参数CHARACTER_SET决定。
- 使用配置文件导出csv时，解析配置文件exp.ini的字符集由${YASDB_HOME}/client/yasc_env.ini中配置的客户端字符集参数CHARACTER_SET决定。
- 导出日期格式数据类型的csv时，解析--date-format参数的字符集由${YASDB_HOME}/client/yasc_env.ini中配置的客户端字符集参数CHARACTER_SET决定。

### 命令选项-Export Options

具体选项如下表所示。

|  选项| 含义|
| --- | --- |
| *-T, --tables* | 导出的表名称，多张表以逗号隔开 |
| *-O, --owner* | 表的所属用户，仅指定一个 |

### 命令选项-CSV Options

具体选项如下表所示，[]表示可选项。

|  选项| 含义|
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *[--fields-enclosed-by]*   | 指定单个字符作为文件中各字段的包围符，该字符取值范围是ASCII码1-126对应字符，换行符、空格除外。默认值`"`，支持用字符、十六进制表示<br/>注意：<br/>\* 数值、bool、rowid、bit类型导出后不带包围符<br/>\* 如果字符类型（char、nchar、varchar、nvarchar）或者时间日期类型（date、time、timestamp、timestamp_ltz、timestamp_tz）的字段中出现了指定的包围符，会使用两个包围符对数据转义后导出<br/>\*指定为null时，所有数据类型均不加包围符导出 |
| *[--fields-terminated-by]* | 支持使用最多五个字符作为字段的分隔符，取值范围是ASCII码1-126对应字符，字母、数字、换行符除外。默认值为`|`，支持用字符、十六进制表示           |
| *[--lines-terminated-by]*  | 使用单个字符char作为行分隔符，该字符可以是ASCII任意一个字符，默认值`\n`                                                                                                                                                                                                     |
| *[--date-format]*          | 支持修改日期格式，与数据库支持的所有日期格式一致，默认值为`yyyy-mm-dd`                                                                                                                                                                                                      |
| *[--time-format]*          | 支持修改时间格式，与数据库支持的所有时间格式一致，默认值为`hh24:mi:ss.ff`                                                                                                                                                                                                   |
| *[--timestamp-format]*     | 支持修改时间戳格式，与数据库支持的所有时间戳格式一致，默认值为`yyyy-mm-dd hh24:mi:ss.ff`                                                                                                                                                                                      |
| *[--timestamp-tz-format]*  | 支持修改时区格式，与数据库支持的所有时区格式一致，默认值为`yyyy-mm-dd hh24:mi:ss.ff tzh:tzm`                                                                                                                                                                                |

#### 参数说明

##### 包围符与分隔符

- 包围符、行分隔符、分隔符彼此不能相同。
- 命令行指定分隔符或包围符时，字符形式应使用单引号包围，如果不进行包围，则需要注意对特殊字符进行转义，十六进制形式无需使用单引号包围。
- 指定字符形式加单引号包围，例如--fields-terminated-by `'"'`、--fields-terminated-by `'@'`。
- 指定字符形式不加引号包围，需要转义的字符：`'`可转义为 `\'\'`，也可转义为`\'`，`` ` ``、`"`、`#`、`&`、`(`、`)`、`*`、`;`、`<`、`>`、`|`、`~`、`\` 均使用 `\` 进行转义，例如--fields-enclosed-by `\"`、--fields-enclosed-by `\&`。
- 指定 --fields-enclosed-by null 不使用包围符时，请确保数据与分隔符无重复，否则会导致导入数据出现错误。
- 可以指定十六进制形式，且无需使用单引号包围，例如--fields-terminated-by `0x01`。
- 命令行指定单引号字符形式作为包围符或分隔符时可转义为 `''`，例如--fields-terminated-by `"''"`、--fields-terminated-by `"'"`、--fields-enclosed-by `\'\'`、--fields-enclosed-by `\'`。
- 命令行指定空格，水平制表符`\t`，换页符`\r`的字符形式作为包围符或分隔符时，使用双引号+单引号包围，例如--fields-terminated-by `"' '"`、--fields-terminated-by `"'$(echo -e "\t")'"`。
- 命令行指定多个字符作为分隔符，使用参考 --fields-terminated-by `"'$(echo -e "\t\r\t")'"`、--fields-terminated-by `"'''"`、--fields-terminated-by `'???'`。

## 使用配置文件导出数据

### 配置模板

可按如下配置模板进行导出选项配置：

```ini
owner = sales
format = csv
user = sys
password = sys
server-host = 127.0.0.1:1688
tables = area,branches
logfile = /home/yasdb
file = /home/yasdb
loglevel = info
fields-enclosed-by = "
fields-terminated-by = ,
```

> **Note**: 
>
> - 配置文件和命令行配置相同的参数，以命令行为准。
> - 同一参数重复配置，以最后一次配置为准。
> - 上述配置文件中的各项值请替换为实际值。
> - 上述路径可配置为相对路径，但不能为../相对路径。
> - 配置文件指定空格、水平制表符`\t`、换页符`\r`的字符形式作为分隔符或包围符时，使用单引号包围，例如fields-terminated-by = `' '`，指定十六进制或单引号作为分隔符或包围符时，不需要包围，例如fields-terminated-by = `''`、fields-terminated-by = `0x02`。
> - 配置文件指定多个字符作为分隔符，使用参考 fields-terminated-by = `'''` 、fields-terminated-by = `'???'`。

### 操作示例

1. 以对YashanDB的sales用户下的样例表area，branches执行导出为例。

2. 新建导出任务配置文件expconfig.ini：

    ```ini
    owner = sales
    format = csv
    user = sys
    password = sys
    server-host = 127.0.0.1:1688
    tables = area,branches
    logfile = /home/yasdb
    file = /home/yasdb
    loglevel = info
    fields-enclosed-by = "
    fields-terminated-by = ,
    ```

3. 执行导出命令exp --csv --config-file expconfig.ini：

    ```shell
    $ exp --csv --config-file expconfig.ini
    YashanDB Export Release 22.2.0.0 x86_64 8f54fba
    [EXPORT] export sales.area succeeded
    [EXPORT] export sales.branches succeeded
    ```

4. 导出路径/home/yasdb下生成按表名命名的CSV数据文件：

    ```shell
    $ ll
    total 1792
    -rw-r-----.  1 yasdb yasdb     121 Nov  6 00:06 area
    -rw-r-----.  1 yasdb yasdb     332 Nov  6 00:06 branches

    $ vi area
    "01","EastChina","Shanghai"
    "02","WestChina","Chengdu
    "03","SouthChina","Guangzhou"
    "04","NorthChina","Beijing"
    "05","CentralChina","Wuhan"
    ```
