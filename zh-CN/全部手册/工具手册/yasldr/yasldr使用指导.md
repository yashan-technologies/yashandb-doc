## 查看帮助

使用yasldr -H命令可查看帮助信息。

```shell
$ yasldr -H
YashanDB Loader Enterprise Edition Release {version_number} x86_64 431125c

YashanDB LOADER HELP:
To specify parameters, should use keywords:
  Command Format:
    YASLDR USERNAME/PASSWORD@IP:PORT KEYWORD=value

    "USERNAME/PASSWORD@URL" must be the first on the command line, "@URL" default 127.0.0.1:1688
    KEYWORD                    DESCRIPTION (DEFAULT)
    ---------------------------------------------------------------------------------------------------
    BATCH_SIZE                 the number of lines per batch, default 4032, range [1, 65535]
    CONTROL_FILE               a file containing the LOAD statement
    CONTROL_TEXT               the LOAD statement, only be used when CONTROL_FILE is not specified
    MODE                       import mode, default BATCH, range [BASIC, BATCH] 
    ......                     ......

  Example:
    YASLDR USERNAME/PASSWORD@IP:PORT CONTROL_FILE=LOAD.CTL
```

## 查看版本

使用yasldr -V命令可查看版本信息。

```shell
$ yasldr -V
YashanDB Loader Enterprise Edition Release {version_number} x86_64 431125c
```

## 导入指令格式

```shell
$ yasldr USERNAME/PASSWORD@IP:PORT {LOAD OPTIONS} {LOAD STATEMENT} 
```
**USERNAME/PASSWORD@IP:PORT**

执行导入命令的数据库用户名、密码、地址，地址默认值为127.0.0.1:1688。

对该用户的权限要求为：

- 对待导入数据的目标表应具备写权限和查询权限。
- 在存算一体分布式集群部署中，用户还应至少为SELECT_CATALOG_ROLE角色并具备ROUTE$，NODE_INFO$，DIST$，OBJ$，DISTCOL$，TAB$，USER$，PARTOBJ$以及PARTCOL$系统表权限。

```sql
-- 为用户授权SELECT_CATALOG_ROLE角色
GRANT select_catalog_role TO USER;

-- 为用户授权相应的系统表权限
GRANT SELECT ON sys.route$ TO USER;
GRANT SELECT ON sys.node_info$ TO USER;
GRANT SELECT ON sys.dist$ TO USER;
GRANT SELECT ON sys.obj$ TO USER;
GRANT SELECT ON sys.distcol$ TO USER;
GRANT SELECT ON sys.tab$ TO USER;
GRANT SELECT ON sys.USER$ TO USER;
GRANT SELECT ON sys.partobj$ TO USER;
GRANT SELECT ON sys.partcol$ TO USER;
```

### LOAD OPTIONS

LOAD OPTIONS部分用来设置运行yasldr的命令行参数。

yasldr提供多个可选的命令行控制参数，方便用户调整和设置导入过程。用户在执行导入命令时，可以同时设置多个参数，例如：

- BATCH_SIZE：导入过程中每批次发送的CSV数据行数。
- MODE：导入方式，可以指定BASIC方式导入或BATCH导入方式。

更多命令行参数的描述见[yasldr命令行参数](yasldr参数说明)。

### LOAD STATEMENT

#### 指定方式

指定LOAD语句，可以为如下方式：

- CONTROL_FILE：含有单条LOAD语句的控制文件。

  示例

  ```shell
  #准备本地CSV文件（以/home/yasdb/area.csv为例）
  $ vi /home/yasdb/area.csv
  12|load|1201|loadbranch|12
  13|load|1301|loadbranch|13
  
  $ vi /home/yasdb/load.ctl
  LOAD DATA INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' optionally enclosed by '"' INTO TABLE branches (branch_no,branch_name,area_no)
  
  $ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 mode=batch packet_size=131072 control_file=/home/yasdb/load.ctl
  YashanDB LOADER VERSION: RELEASE 1.0.0.0
  [YASLDR] import succeeded
  ```

- CONTROL_TEXT：以双引号+单引号包围的单条LOAD语句，只有不指定CONTROL_FILE且指定CONTROL_TEXT时才被选用。

  示例

  ```shell
  #准备本地CSV文件（以/home/yasdb/area.csv为例）
  $ vi /home/yasdb/area.csv
  12|load|1201|loadbranch|12
  13|load|1301|loadbranch|13
  
  $ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' optionally enclosed by '\"' INTO TABLE area (area_no,area_name,dhq) '"
  YashanDB LOADER VERSION: RELEASE 1.0.0.0
  [YASLDR] import succeeded
  ```

> **Note**: 
>
> 使用CONTROL_TEXT指定时，需同时满足操作系统对特殊字符的转义要求，例如在Linux下对双引号需使用\转义。

#### 语句内容

##### load data

```ebnf+diagram
syntax::= LOAD DATA [option_clause]
(input_file_clause [bad_file_clause] [discard_file_clause])
{" " (input_file_clause [bad_file_clause] [discard_file_clause])}
table_clause
```

##### option\_clause

```ebnf+diagram
syntax::= OPTIONS "(" (("PARAMETER_NAME" "=" PARAMETER_VALUE)){","("PARAMETER_NAME" "=" PARAMETER_VALUE)} ")"
```

该语句用于指定加载操作的相关选项，下列是一些常使用的参数：

###### COMMIT\_ROWS

决定事务的提交频率，假设其值为n，解码数据文件中n条数据，提交一次事务，BULKLOAD导入例外。

parameter_value须为一个范围在[1,4294967295]之间的整数，默认值为4096。

##### DEGREE\_OF\_PARALLELISM

指定数据加载任务的并行度，受目标端数据库参数MAX_PARALLEL_WORKERS限制，若degree_number大于该参数，以该参数为准。

parameter_value须为一个范围在[2,256]之间的整数，默认值为8。系统会结合该参数值、硬件环境、数据文件大小等计算实际的并行度值，并使加载任务按实际值并发运行。

##### ERRORS

指定数据导入的容错数据条数上限，当数据错误条数达到上限时，终止数据导入。多个文件导入时，错误数据条数累加计算。

parameter_value须为一个范围在[0,4294967295]之间的整数，默认值为50。

更多详细参数描述见[yasldr参数说明](yasldr参数说明)。

##### input\_file\_clause

```ebnf+diagram
syntax::= INFILE input_file_path
[FIELDS file_type]
[WITH EMBEDDED|WITHOUT EMBEDDED]
[FIELDS TERMINATED BY fields_terminated_char]
[OPTIONALLY ENCLOSED BY enclosed_by_char]
```

该语句指定导入文件的相关信息。可同时指定多个导入文件，以空格分隔。

支持使用*和?通配符进行文件名称的正则匹配，*用于匹配0个或任意多个字符，?用于匹配单个字符（即占位一个字符）。具体匹配规则如下：

- 若INFILE指定的文件与通配符匹配的文件重名，则会报重名错误。
- 若匹配到目录，则直接跳过，不会递归处理。
- 单次最多匹配250个文件，若匹配的文件数超过上限，则会报错。

示例

```bash
#导入/home/yasdb目录下以.csv结尾的所有文件
$ yasldr sales/sales@127.0.0.1:1688 control_text="'LOAD DATA INFILE '/home/yasdb/*.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq)'"

#导入/home/yasdb目录下csv10-csv20之间的所有文件
$ yasldr sales/sales@127.0.0.1:1688 control_text="'LOAD DATA INFILE '/home/yasdb/csv1?.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq)'"
```

###### INFILE input\_file\_path

导入文件的路径及名称，支持指定绝对和相对路径。

该语句指定的导入文件必须存在，且系统用户必须拥有对其的读取权限，否则加载任务报错退出。

可以指定一个空数据文件，不影响加载任务的继续运行。

###### FIELDS file\_type

该语句用于指定导入文件的类型，可省略，省略则默认为CSV文件。

file_type的值只可以为CSV，否则加载任务报错退出。

###### WITH EMBEDDED|WITHOUT EMBEDDED

该语句用于指定换行符是否可被包围符包围作为数据，可省略，省略则默认为WITHOUT EMBEDDED，即换行符不可作为数据。

###### FIELDS TERMINATED BY fields\_terminated\_char

该语句用于指定导入数据列的分隔符，不指定时默认将逗号作为分隔符。

fields_terminated_char支持以下表示方法：

- 最多五个单字节字符
- 单个十六进制
- 单个整数

取值范围：ASCII码1-126对应的字符，数字、换行符、大小写字母除外。

###### OPTIONALLY ENCLOSED BY enclosed\_by\_char

该语句用于指定导入数据的包围符，可省略，省略时系统默认为按双引号包围。

enclosed_by_char支持以下表示方法：

- 单个单字节字符
- 单个十六进制
- 单个整数

取值范围：ASCII码1-126对应的字符，换行符、空格除外。

> **Note**: 
>
>- 包围符与分隔符不能相同。
>- 指定分隔符或包围符时，请注意命令行输入的转义，例如`\`转义为`\\`，`"`应转义为 `\"`，`'`应转义为`''`， `$`应转义为`\$`。
>- 指定分隔符为空格时，不支持字符表示，应使用十六进制或整数表示。

##### bad\_file\_clause

```ebnf+diagram
syntax::= BADFILE bad_file_path
```

该语句用于指定错误数据文件的地址，可省略，省略则默认在input_file_clause语句指定的导入文件目录下生成与导入文件同名但后缀为bad的数据文件，若存在同名文件将会直接覆盖。

无论是否指定本语句，只要导入错误数据就会生成相应的错误数据文件，该文件中可写入的错误数据条数受ERRORS参数限制。

错误数据不会被导入至数据库中，下述数据会被写入错误数据文件中：

- 类型转换失败的数据
- 违反约束的数据
- 不符合CSV格式的数据
- 未命中分区的数据

示例

```bash
#准备本地CSV文件（以/home/yasdb/area.csv为例）
$ vi /home/yasdb/area.csv
  "1"|"1"
  "nihao"
  "hello"

#创建表bad_load表
CREATE TABLE bad_load(c1 int,c2 int);

#执行导入命令
$ yasldr sales/sales@127.0.0.1:1688 control_text="'LOAD DATA INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' BADFILE '/home/yasdb/area.bad' INSERT INTO TABLE bad_load(c1,c2)'"

#由于"nihao"和"hello"无法转换成int类型数据，如上两种数据会被记录在错误数据文件中，于终端中执行如下命令查看/home/yasdb/area.bad数据文件内容
$ cat /home/yasdb/area.bad
"nihao"
"hello"
```

##### discard\_file\_clause

```ebnf+diagram
syntax::= DISCARDFILE discard_file_path [(DISCARDS|DISCARDMAX) discard_number]
```

该语句用于指定存放过滤数据的地址，与input_file_clause语句指定的导入文件相关，可省略，省略则默认不创建过滤数据文件。

即使指定该语句，仍需导入数据满足过滤条件才会生成相应的过滤数据文件，过滤数据不会被导入至数据库中。

过滤条件如下：

- 整行映射均为NULL的数据。

###### (DISCARDS|DISCARDMAX) discard\_number

该语句用于指定数据导入的过滤上限，当数据被过滤的数量达到上限时，终止数据导入，可省略，省略则默认为UINT64_MAX。

如未指定discard_file_clause语句但指定了DISCARDS|DISCARDMAX关键字，将在input_file_clause语句指定的导入文件目录下生成与导入文件同名但后缀为dsc的数据文件，若存在同名文件将会直接覆盖。

##### table\_clause

```ebnf+diagram
syntax::= [load_type] (INTO TABLE ([schema "."] table_name) [TRAILING NULLCOLS] column_clause)
{" " (INTO TABLE ([schema "."] table_name) [TRAILING NULLCOLS] column_clause)} [directory_clause]
```

该语句用于指定导入目标表的相关信息。

###### TRAILING NULLCOLS

当导入文件中的数据列少于目标表的列数时，指定TRAILING NULLCOLS可以对缺少映射数据的列补NULL值（如果该列上存在一些不允许NULL值的约束定义，补NULL值仍会导致错误），否则加载任务将报错。

当导入文件中的数据列多于目标表的列数时，多余的数据列会被自动忽略且不会抛出错误，这种行为与trailing nullcols配置参数无关。

###### column\_clause

```ebnf+diagram
syntax::= "(" (table_column_name (lob_column_clause|lls_column_clause|func_column_clause) |filler_column_clause)
{"," (table_column_name (lob_column_clause|lls_column_clause|func_column_clause)  |filler_column_clause)} ")"
```

该语句用于指定待导入表中的列与CSV文件的映射关系，其中table_column_name用于指定需要导入数据的列名。

当导入的目标表有虚拟列时：

- 如果CSV文件本身不包括虚拟列数据，直接指定列导入；

- 如果CSV文件包括了虚拟列数据，需要指定实体列在CSV文件中的位置跳过虚拟列不做导入，或指定虚拟列为`FILLER`。

**filler_column_clause**

```ebnf+diagram
syntax::= filler_column_name FILLER
```

该语句用于构造伪列，该伪列用于映射CSV文件中的一列数据。允许存在未被引用的FILLER伪列，此时伪列所对应的CSV数据列将跳过而不被处理，但建议配套使用filler_column_clause和lob_column_clause语句。

**lob_column_clause**

```ebnf+diagram
syntax::= LOBFILE "(" filler_column_name ")" [terminated_by_eof]
```

该语句用于将列的导入模式指定为LOBFILE模式，该模式下通过引用FILLER伪列指向的LOB数据文件，将整个文件导入至目标表指定的列中。 

**lls_column_clause**

```ebnf+diagram
syntax::= LLS 
```
该语句用于将列的导入模式指定为LLS（Lob Location Specifier）模式，通过指定LLS关键词选择该导入模式。该模式选取数据文件的部分内容进行导入，且可指定从任意位置和任意长度开始导入。 

指定了LLS关键字的目标表列字段映射的导入文件数据列格式需为`filename.ext.nnn.mmm/`： 

- filename.ext：包含LOB数据的文件名称，其中filename部分支持绝对路径和相对路径，且路径部分支持点（.）号，而文件名称部分不能包含点（.）号。 
- nnn：文件中LOB数据的字节的偏移，仅允许为空、0或正整数且不得超过数据文件的大小。
  - 值为正整数时，实际偏移量 = 输入值 - 1。
  - 值为空或0时，实际偏移量 = 0。
  - 值为负数时返回错误。
-  mmm：字节中LOB的长度，仅允许为空、-1、0或正整数。
  - 值为正整数时，表示导入的实际字节长度。
  - 值为-1时，表示NULL。
  - 值为空或0时，表示导入一个空LOB。
- 正斜杠（/）为终止字符，格式中必须包含该字符，否则报错。

**func_column_clause**

```ebnf+diagram
syntax::= '"' func_name "(" (func_arg) {"," (func_arg)} ")" '"' 
```

yasldr支持导入指定函数的计算值数据，最终插入的数据是经过函数计算后的结果，目前仅支持ST_GeomFromText、BFILENAME函数，且不能嵌套使用函数。

该语句用于指定函数及其参数，仅支持在BASIC导入模式下使用。

- func_name用于指定函数名。
- func_arg用于指定函数参数，可以使用`?`表示对应CSV文件中的数据列，其他任何形式的参数将会完整的传递给函数。

示例1：行外LOB数据导入

```bash
#准备本地lob1.dat和lob2.dat文件，文件位于/home/yasdb/，内容均为：
abcdefg

#创建/home/yasdb/load_lob.csv文件，内容为：
"1"|"lob1.dat"|"lob1.dat.1.7/"

#创建表bad_load表
CREATE TABLE sqlldr_lob(c1 INT,c2 CLOB,c3 CLOB);

#执行导入指令
$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA INFILE '/home/yasdb/load_lob.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' INTO TABLE sqlldr_lob(c1,file1 filler,c2 lobfile(file1), c3 LLS)'"
```

示例2：GIS数据导入

```bash
#准备gis数据文件load_gis_demo.csv，其中部分gis列坐标值和坐标系值相邻存放，部分GIS列只有坐标值，内容如下：
1,"POINT(0 0)", 4326,11,"POINT(0 0)", "yashandb"
2,"LINESTRING(1 2,4 5)", 2018,22,"LINESTRING(1 2,4 5)", "postgresql"
3,"POLYGON ((1 0,1 1,2 2,1 0),(0 0,6 6,8 8,0 0))", 2025,33,"POLYGON ((1 0,1 1,2 2,1 0),(0 0,6 6,8 8,0 0))", "mysql"
4,"MULTIPOINT ((1 1),(2 2))", 4326,44,"MULTIPOINT ((1 1),(2 2))", "yashandb"

#创建待导入的GIS表
CREATE TABLE YASLDR_LOAD_GIS_DEMO (C1 INT, C2 ST_GEOMETRY, C3 INT, C4 ST_GEOMETRY, C5 VARCHAR(20));

#执行导入指令
$ yasldr sales/sales mode=basic control_text="'load data options(degree_of_parallelism=3) infile './load_gis_demo.csv' into table YASLDR_LOAD_GIS_DEMO(c1, c2 \"ST_GeomFromText(?,?)\", c3, c4 \"ST_GeomFromText(?, 3256)\", c5)'"
```

示例3：BFILE数据导入
```bash
#准备含有bfile列的数据文件load_bfile.csv，内容如下：
1|"MY_DIR"|"a.txt"|"sss1"|"MY_DIR"|"b.txt"|"MY_DIR"|"b1.txt"|"lobdata1"
2|"MY_DIR"|"testfile"|"sss2"|"MY_DIR"|"testimage"|"MY_DIR"|"b2.txt"|"lobdata2"

#创建待导入的BFILE表
CREATE TABLE T_BFILE(C1 INT,C2 BFILE,C3 VARCHAR(10),C4 BFILE, C5 BFILE, C6 CLOB);

#执行导入指令
$ yasldr sales/sales mode=basic control_text="'load data OPTIONS(degree_of_parallelism=3) infile './load_bfile.csv' fields terminated by '|' optionally enclosed by '\"' append into table t_bfile (c1, c2 \"BFILENAME(?,?)\", c3, c4 \"BFILENAME(?,?)\", c5 \"BFILENAME(?,?)\", c6)'"
```

示例4：对含有虚拟列的表进行数据导入（HEAP表）

```sql
-- 在目标数据库中创建包含虚拟列的业务表
CREATE TABLE tab_virtual_test (c1 INT, v1 AS (c1 + c2), c2 INT);
```

```bash
# 在yasldr工具端准备CSV文件virtual_col.csv
$ echo '"1","2","5"' >> $YASDB_DATA/c5csv/virtual_col.csv
$ echo '"6","2","5"' >> $YASDB_DATA/c5csv/virtual_col.csv
$ echo '"7","2","5"' >> $YASDB_DATA/c5csv/virtual_col.csv

# CSV文件包括了实体列和虚拟列数据，指定实体列C1、C2在CSV文件的位置进行导入，导入模式必须为basic
$ yasldr user_name/passwd control_text="'LOAD DATA INFILE '?/c5csv/virtual_col.csv' FIELDS TERMINATED BY ',' optionally enclosed by '\"' INTO TABLE tab_virtual_test (C1 COLUMN(1),C2 COLUMN(3)) '" mode=basic

# CSV文件包括了实体列和虚拟列数据，指定虚拟列跳过的方式导入数据，导入模式必须为basic
$ yasldr user_name/passwd control_text="'LOAD DATA INFILE '?/c5csv/virtual_col.csv' FIELDS TERMINATED BY ',' optionally enclosed by '\"' INTO TABLE tab_virtual_test (C1, V1 FILLER, C2)'" mode=basic
```

###### directory\_clause

该语句用于指定run_level选项拆分后的文件存放目录，可省略，省略时默认以infile的第一个文件所在的目录作为输出目录。

示例

```bash
#于/home/yasdb目录下创建file文件夹

#指定directory_clause
$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA OPTIONS(RUN_LEVEL=SPLIT,DEGREE_OF_PARALLELISM=2) INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq) directory '/home/yasdb/file''"

#result：拆分后的文件会存放在/home/yasdb/file目录中

#不指定directory_clause
$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA OPTIONS(RUN_LEVEL=SPLIT,DEGREE_OF_PARALLELISM=2) INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq) '"

#result：拆分后的文件会存放在/home/yasdb目录中
```

## 导入详情介绍

### 导入前配置

yasldr工具在BATCH模式下执行导入时会通过批量绑定参数插入的方式直接与存储层数据结构对接，为提升性能，需要在导入前对目标端数据库的一些参数进行合理的配置，具体参数配置如下：

|  配置参数项| 建议操|
|----------------------|----------------------------------------|
| LARGE_POOL_SIZE          | 调整为参数允许范围内的最大值，若导入过程出现no free blocks in large pool错误，表示该值仍不能满足导入资源要求，解决方案：<br>1. 调整导入表，减少导入heap分区表的分区总个数<br>2. 降低导入时的degree_of_parallelism参数<br>3. 重新导入     |
| WORK_AREA_STACK_SIZE     | reader线程数 = degree_of_parallelism / (decoder_thread_times + 1)，向上取整<br>sender线程数 = degree_of_parallelism - reader线程数<br>SIZE = 512KB + 126KB * 表个数 * sender线程数 |
| WORK_AREA_POOL_SIZE      | MIN_SIZE = 线程数量 * 表数量 * 分区数量 * 256B + reader线程数 * sender线程数 * 1KB           |
| DATA_BUFFER_SIZE         | 指定数据缓存区的大小。数据缓存区容量越大，导入性能越好，默认值为64M，取值范围为[32M,64T]      |
| COLUMNAR_BUFFER_SIZE     | 当LSC表以BULKLOAD模式导入时，建议调大此参数，默认值为2G，取值范围为[256M,4T]              |
| COLUMNAR_DATA_BUFFER_PERCENT    | 指定LSC存储引擎使用的数据缓存区占COLUMNAR_BUFFER_SIZE大小，当LSC表以BULKLOAD模式导入时，此参数配置值越大，导入性能越好，此参数配置值越小，可以增加计算性能，默认值为40，取值范围为[1,99]      |

>**Note**:
> 
> 其他可能规避性能下降的方法：
>
> - 如果因待导入的数据中的表创建了索引引起性能下降明显，可考虑先导入数据再手动创建索引。
>
> - 导入过程中触发表空间自动扩展会影响性能，建议在导入数据前根据CSV文件大小提前完成表空间的扩展。
>
> - 在存算一体分布式集群部署中，使用bulkload模式导完数据后，建议对表执行compact和收集统计信息等操作，以便提升后续的查询等性能。

### 导入

yasldr工具的导入过程为并行导入，会依据`Load statements`中设置的`DEGREE_OF_PARALLELISM`参数值开启多个线程进行并行导入。

导入过程中涉及的线程分为三类：

- CONTROLLER线程：即主线程，负责`Load Options`和`Load Statement`的解析，将待导入的CSV数据文件进行切分，启动READER线程和SENDER线程进行数据导入。
- READER线程：负责读取和解析CSV文件，对数据进行解码，并按照分区组织数据后，将数据交给SENDER线程发送。
- SENDER线程：负责将READER线程提交的数据发送到服务端，并解析处理服务端返回的消息。

### 导入日志

导入执行结束后，会显示诸如`Check /home/yasdb/area_stats.log for more info.`的日志路径信息，该信息中显示的日志打印了本次导入的目标表名、表字段和CSV数据列的映射关系、错误数据的位置、错误原因以及统计信息等信息。

示例

```bash
YashanDB Loader Enterprise Edition Release {version_number} x86_64 a533f66
Production on 2023-10-13 11:06:58.212

Table AREA:
column_name                                                        infile_column
AREA_NO                                                            1
AREA_NAME                                                          2
DHQ                                                                3

infile /home/yasdb/area_stats.csv offset 1 is rejected by table AREA because
type convert error : not a valid number.

Table AREA:
  2 logical rows read.
  1 rows successfully loaded.
  1 rows not loaded due to data errors.
  0 rows not loaded because all fields were null.
```

<span id="statistic" name="statistic" class="yaslink"></span>

### 统计信息

导入生成的日志中会包含本次导入过程中的执行统计情况，输出的统计信息包含：

**概述**

统计信息分为CONTROLLER的统计信息、READER统计信息、SENDER统计信息，在分布式情况下，还存在NODE统计信息。

**CONTROLLER统计信息**

CONTROLLER部分展示READER和SENDER线程的数量、解析耗时和准备执行环境的耗时。

**READER统计信息**

READER统计信息部分展示IO次数、IO耗时、解码的行数、行最大宽度、行平均宽度、读取行的总耗时、解码总耗时、分区计算总耗时、行转换总耗时、等待分区可用总耗时、列转换总耗时、发送队列总耗时以及线程的总运行耗时。

**SENDER统计信息**

SENDER统计信息部分展示发送次数、发送总耗时、单次最大发送耗时、发包总耗时、执行提交的次数、提交总耗时、获取发送单元的总耗时以及线程总运行耗时。

**NODE统计信息**

NODE统计信息部分展示发送到节点的行数、发送的次数、发送总耗时、单次最大发送耗时、提交次数、提交总耗时和节点的地址信息。

示例

```bash
#area_stats.csv文件内容为:
1|load|101|loadbranch|1
2|load|201|loadbranch|2
3|load|301|loadbranch|3
4|load|401|loadbranch|4
5|load|501|loadbranch|5
6|load|601|loadbranch|6

#area创建语句
CREATE TABLE area(
area_no   INTEGER,
area_name VARCHAR(10),
dhq       INTEGER);

$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2,STATS=TRUE) INFILE '/home/yasdb/area_stats.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq)'"

#执行后统计信息示例
Reader Execution Statistics:
   id    io_cnt     io_cost    avg_cost     lines   max_len   min_len   avg_len  fetch_cost  decode_cost locate_cost  to_row_cost    wait_cost  to_col_cost   enque_cost      elapsed
    0         1           0           0         6        24        24        24           0            0           0            0         1133            0            0         2225

Sender Execution Statistics:
   id    send_cnt   send_cost    max_cost packet_cost  commit_cnt commit_cost  deque_cost     elapsed
    0           6       49718       15515       49718           2      192412           0      395141

Node Send Statistics:
   id       lines    send_cnt   send_cost    max_cost  commit_cnt commit_cost                       address
    0           3           6       49718       15515           2      192412            xxx.xxx.xxx.xxx:xxxx
    1           3           6       49718       15515           2      192412            xxx.xxx.xxx.xxx:xxxx
```

### 导入结果验证

导入结束后，可以对结果进行验证，检查数据是否全部正确导入。

可以通过对比CSV文件中的数据条数和数据库表中的数据条数是否一致来进行验证。

示例
```bash
#area_stats.csv文件内容为:
8|load|801|loadbranch|8
9|load|901|loadbranch|9

#area创建语句
CREATE TABLE area(
area_no   INTEGER,
area_name VARCHAR(10),
dhq       INTEGER);

$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2,STATS=TRUE) INFILE '/home/yasdb/area_stats.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq)'"
YashanDB Loader Enterprise Edition Release {version_number} x86_64 a533f66
2 rows successfully loaded.
Check /home/yasdb/area_stats.log for more info.
[YASLDR] execute succeeded

#计算文件中的数据条数
$ wc -l area_stats.csv
2 area_stats.csv

#查询数据库表中的数据条数是否和文件条数一致
$ yasql sales/********@127.0.0.1:1688 -c "select count(*) from area"

             COUNT(*)
---------------------
                    2

```
