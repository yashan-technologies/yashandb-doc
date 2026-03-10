## 通用描述

LOAD DATA用于将CSV文件中的数据加载到数据库的物理表中。

如需通过yasql工具执行LOAD DATA语句导入文件，用户需拥有FILE权限，且操作的目标文件需在SECURE_FILE_PRIV参数指定的安全目录下，可通过SHOW PARAMETER SECURE_FILE_PRIV查看安全目录。

本语句为SQL级别的数据加载（SQL LOADER），等同于对目标表执行DDL操作，因此需满足目标表上已定义的一切约束限制；且也会对目标表加行锁，需确保在执行数据加载前要操作的行上无未提交事务，否则会由于等待行锁而导致加载任务被挂起。

本语句在执行初始阶段会执行一次事务提交，以避免在同一事务中与执行本语句之前的其它语句产生事务等待而导致事务挂起。

存算一体分布式集群部署中用户无法执行本语句。

**导入数据的事务提交**

在加载过程中，SQL LOADER的事务提交以存放CSV数据的缓存区为单位。当存放CSV数据的缓存区被存满之后，执行一次当前事务的提交。

CSV数据缓存区以一条完整的数据行开始，以一条完整的数据行结束，缓存区中的数据长度视具体情况而定，但不超过2M字节。

若缓存区中存在一行数据插入失败，则整个缓存区数据插入失败，回滚当前事务。  

**设置nologging导入**

如果是在数据迁移场景通过本语句执行数据导入，通过在options参数中指定nologging为true，可以实现nologging导入。

只建议在数据迁移场景设置nologging属性，且需要关注如下事项：

- 主备环境不可启用nologging，需要在完成数据导入后再建备库。
- 使用nologging导入后，执行一次全量checkpoint可以保证数据持久性，否则宕机后可能导致数据丢失。
- 当表的属性为nologging，同时发生宕机时，重启后该表将被置为corrupted，只能通过truncate表进行恢复，且需要重新执行上述导入过程以恢复数据。
- 如果一个事务失败，该事务内所有执行过插入数据操作的nologging表都会被标记为corrupted。
- nologging属性的LSC表使用bulkload模式导入数据时，不受nologging属性影响（性能无变化，失败也不会被标记为corrupted）。
- 当表为nologging时，不能对其执行update和delete操作，导入操作完成后需及时将其修改为logging。
- 设置表状态为nologging属于DDL操作，会加表锁。如果并发执行DML语句，可能导致DML语句失败。
- 当表为nologging，或使用nologging方式导入时，在违反约束条件时可能出现容错失败的现象，具体受违反约束的数据在所有数据中的位置影响。

**导入环境准备**

用户实际配置值应大于该值，建议导入结束后，依据在线业务的要求重新调整这些值。

|  配置参数项| 建议操作|
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LARGE_POOL_SIZE      | 调整为参数允许范围内的最大值，之后导入过程如出现no free blocks in large pool错误，表示该值仍不能满足导入资源要求，此时需：<br>1. 调整导入表，减少导入heap分区表的分区总个数。<br>2. 降低导入时的degree_of_parallelism参数后重新导入。                                                                                                  |
| WORK_AREA_STACK_SIZE | reader线程数 = degree_of_parallelism / (decoder_thread_times + 1)，向上取整。<br>decoder线程数 = degree_of_parallelism - reader线程数。<br>SIZE = 512KB + 126KB * 表个数 * decoder线程数。 |
| WORK_AREA_POOL_SIZE  | MIN_SIZE = 线程数量 * 表数量 * 分区数量 * 256B + reader线程数 * decoder线程数 * 1KB。                                                                                                                                                                                 |

语句定义
----

**load data::=**

```ebnf+diagram
syntax::= LOAD DATA [option_clause]
(input_file_clause [bad_file_clause] [discard_file_clause])
{" " (input_file_clause [bad_file_clause] [discard_file_clause])}
table_clause
```

**[option_clause](#optionclause)::=**

```ebnf+diagram
syntax::= OPTIONS "(" [(PARAMETER_NAME "=" PARAMETER_VALUE) {"," (PARAMETER_NAME "=" PARAMETER_VALUE)}] ")"
```

**[input_file_clause](#inputfileclause)::=**

```ebnf+diagram
syntax::= INFILE input_file_path
[FIELDS file_type]
[WITH EMBEDDED|WITHOUT EMBEDDED]
[FIELDS TERMINATED BY fields_terminated_char]
[OPTIONALLY ENCLOSED BY enclosed_by_char]
```

**[bad_file_clause](#badfileclause)::=**

```ebnf+diagram
syntax::= BADFILE bad_file_path
```

**[discard_file_clause](#discardfileclause)::=**

```ebnf+diagram
syntax::= [DISCARDFILE discard_file_path] [(DISCARDS|DISCARDMAX) discard_number]
```

**[table_clause](#tableclause)::=**

```ebnf+diagram
syntax::= [load_type] (INTO TABLE ([schema "."] table_name) [when_clause] [TRAILING NULLCOLS] column_clause)
{" " (INTO TABLE ([schema "."] table_name) [when_clause] [TRAILING NULLCOLS] column_clause)} [directory_clause]
```

**[load_type](#loadtype)::=**

```ebnf+diagram
syntax::= INSERT|APPEND
```

**[when_clause](#whenclause)::=**

```ebnf+diagram
syntax::= WHEN load_condition_clause {AND load_condition_clause}
```

**[load_condition_clause](#loadconditionclause)::=**

```ebnf+diagram
syntax::= ["("] (table_column_name|"(" csv_column_order ")") compare_oper "'column_value'" [")"]
```

**[compare_oper](#compareoper)::=**

```ebnf+diagram
syntax::= "="|"!="|"<>"
```

**[column_clause](#columnclause)::=**

```ebnf+diagram
syntax::= "(" (normal_column_clause|filler_column_clause|lob_column_clause|lls_column_clause)
{"," (normal_column_clause|filler_column_clause|lob_column_clause|lls_column_clause)} ")"
```

**[normal_column_clause](#normalcolumnclause)::=**

```ebnf+diagram
syntax::= table_column_name [COLUMN "(" csv_column_order ")"] [nullif_clause] 
```

**[nullif_clause](#nullifclause)::=**

```ebnf+diagram
syntax::= NULLIF load_condition_clause {AND load_condition_clause}
```

**[filler_column_clause](#fillercolumnclause)::=**

```ebnf+diagram
syntax::= filler_column_name FILLER [COLUMN "(" csv_column_order ")"] 
```

**[lob_column_clause](#lobcolumnclause)::=**

```ebnf+diagram
syntax::= table_column_name LOBFILE "(" filler_column_name ")" [terminated_by_eof] [nullif_clause] 
```

**[lls_column_clause](#llscolumnclause)::=**

```ebnf+diagram
syntax::= table_column_name [COLUMN "(" csv_column_order ")"] [nullif_clause] LLS 
```

**[directory_clause](#directoryclause)::=**

```ebnf+diagram
syntax::= directory split_file_path
```

<span id="optionclause" name="optionclause" class="yaslink"></span>

### 1. option\_clause

该语句让用户指定加载操作的相关选项，多个选项间用逗号（`,`）隔开。可省略，即不由用户指定任何选项，等同于OPTIONS()。

**COMMIT_ROWS**

决定事务的提交频率，假设其值为n，解码数据文件中n条数据，提交一次事务。

parameter_value须为一个范围在[1,4294967295]之间的整数，默认值为4096。

**DECODER_THREAD_TIMES**

决定线程的分配比，在yasldr客户端导入时意为READER线程与SENDER线程的比值，在LOAD DATA导入时意为READER线程和DECODER线程的比值，两种线程的总和不超过DEGREE_OF_PARALLELISM。

parameter_value须为一个范围在[1,255]之间的整数，默认值为7。

**DEGREE_OF_PARALLELISM**

指定数据加载任务的并行度，服务端模式受参数MAX_PARALLEL_WORKERS限制，若degree_number大于该参数，以该参数为准。

parameter_value须为一个范围在[2,256]之间的整数，默认值为8，该数值代表了用户期望的并行度值，系统会结合用户期望值、硬件环境、数据文件大小等计算实际的并行度值，并使加载任务按实际值并发运行。

**ENABLE_BULK**

指定HEAP表和LSC表的导入模式。

parameter_value须为TRUE或FALSE，默认值为FALSE。

当其值为TRUE时，HEAP表使用bcp模式导入，LSC表使用bulkload模式导入。

**ENABLE_DEDUP**

指定LSC表的冲突处理方式。

该选项必须与ENABLE_BULK选项同时使用，parameter_value须为TRUE或FALSE，默认值为FALSE。

当其值为TRUE时，LSC表对导入过程中产生唯一约束冲突的数据会进行去重处理。

**ERRORS**

指定数据导入的容错数据条数上限，当数据错误条数达到上限时，终止数据导入。多个文件导入时，错误数据条数累加计算。

parameter_value须为一个范围在[0,4294967295]之间的整数，默认值为50。

**NOLOGGING**

当其值为TRUE，对于语句中声明的状态为LOGGING的表，会将其转为NOLOGGING进行导入，导入结束后，将恢复表的LOGGING状态；当其值为FALSE，不对表的LOGGING状态做操作。

若表的初始状态为LOGGING，导入完成后需恢复表的LOGGING状态，机制为异步，在后台进行表的LOGGING状态转换，该行为可能失败，转换结果通过运行日志记载，详情参见LOGGING ASYNC动作。

parameter_value需为TRUE或FALSE，默认值为FALSE。

**NULL_LOB_FORMAT_SIZE**

值为1，表示特定的LOB型NULL值表示，该模式下会组织成4B全F，只在LLS导入适配下使用；值为0，表示正常的LOB型NULL值表示，该模式下会组织成8B全F，是一种通用的LOB型NULL表示方式。

parameter_value只有0或1，其他值无效，默认值为0。

**RUN_LEVEL**

指定程序执行的任务类型，可指定SPLIT或SPLIT_TO_PART模式。

RUN_LEVEL选项仅在使用yasldr工具进行数据导入时实现，无法直接通过LOAD DATA语句指定该选项，具体操作可参考[yasldr使用指导](../../../工具手册/yasldr/yasldr使用指导)章节描述。

parameter_value须为SPLIT或SPLIT_TO_PART，不指定RUN_LEVEL情况下默认不进行文件拆分操作。

**LOB_PATH_RELATIVE**

指定进行文件拆分时，outline lob导入形式的LOB文件路径类型。

parameter_value须为TRUE或FALSE，默认值为FALSE。

- 如果指定为TRUE，则进行文件拆分时，仅支持LOB文件指定为相对路径。若指定为绝对路径，执行语句会报错。
- 如果指定为FALSE，则进行文件拆分时，LOB文件可以指定为相对路径或绝对路径。若指定为绝对路径，yasboot不会将数据拷贝到远端节点。

**LOG**

指定生成日志文件，记录执行数据加载任务的过程中过程信息，包括数据导入失败原因，导入成功条数等信息，与input_file_clause语句指定的导入文件相关。无论是否指定本参数，均会生成日志文件，省略则默认在首个导入文件目录下生成与该文件同名，后缀为log的日志文件。

parameter_value支持相对路径及绝对路径，仅支持指定到文件名。

> **Note**:
>
> YashanDB数据导入生成日志文件的相关参数有SILENT、STATS和LOG，三种参数的关系如下：
>
> - SILENT为TRUE时，STATS和LOG参数失效，此时不会生成导入日志、错误数据文件和过滤数据文件。
> - SILENT为FALSE时：
>   - STATS为TRUE，日志文件内包含统计信息。
>   - STATS为FALSE，日志文件内不包含统计信息，仅包含过程信息。

示例（单机、共享集群部署）

```sql
--自行创建area_log.csv文件，文件位于/data目录下，文件内容为:
1|load|101|loadbranch|1
2|load|201|loadbranch|2

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2,LOG='/data/area_log.log')
INFILE '/data/area_log.csv' fields terminated BY '|'
INTO TABLE area(area_no,area_name);

--日志文件内容
YashanDB Loader Release {version_number} x86_64 ff1fe7a
Production on 2023-08-24 04:34:29.409

Table AREA:
column_name                                                        infile_column
AREA_NO                                                            1
AREA_NAME                                                          2
DHQ                                                                NULL

Table AREA:
  2 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.

Total logical records read: 2
Total logical records rejected: 0
Total logical records discarded: 0

Elapsed time was: 00:00:00.07
```

**SILENT**

指定导入过程中是否生成记录文件，包括日志文件、错误数据文件及过滤数据文件。当其值为TRUE，表示不生成记录文件；值为FALSE，表示生成记录文件。

parameter_value须为TRUE或FALSE，默认值为FALSE。

**SINGLE_PART**

指定导入文件中的数据是否属于同一个分区，如果属于同一分区，将简化分区计算提升性能，指定为TRUE时，请确保导入文件中的数据属于相同分区。

parameter_value须为TRUE或FALSE，默认值为FALSE。

本参数仅支持于yasldr工具中指定。

**STATS**

指定是否打印导入过程中的统计信息。值为TRUE时，会将导入过程中的统计信息打印输出到日志中；值为FALSE时，不会打印统计信息。打印输出信息的日志会保存至导入数据文件所在的路径中，且与该数据文件同名，文件格式为log格式。

parameter_value须为TRUE或FALSE，默认值为FALSE。

具体打印的统计信息如下：

- 导入对象
- READER、DECODER线程的数量、时间和内存
- 存储的时间及内存
- 总时间及内存

示例（单机、共享集群部署）

```sql
--自行创建area_stats.csv文件，文件位于/data目录下，文件内容为:
1|load|101|loadbranch|1
2|load|201|loadbranch|2

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2,STATS=TRUE)
INFILE '/data/area_stats.csv' fields terminated BY '|'
INTO TABLE area(area_no,area_name);

--日志内容节选
YashanDB Loader Release 23.1.0.100 x86_64 1a34179
Production on 2023-07-31 20:18:59.849

Table AREA:
column_name                                                        infile_column
AREA_NO                                                            1
AREA_NAME                                                          2
DHQ                                                                NULL

In this Task, degree of parallelism is 2.
The number of reader is 1.
The number of decoder is 1.
```

**TRIM**

表示对CSV数据文件中空格的处理情况。LDRTRIM表示修剪数据列起始的空格、制表符，NOTRIM表示不对数据列起始的空格、制表符做处理。对于有包围符的数据，包围符内的字符均视为数据，不受该参数约束。

parameter_value须为LDRTRIM或NOTRIM，默认值为LDRTRIM。

示例（单机、共享集群部署）

```sql
--自行创建area.csv文件，文件位于/data目录下，文件内容为:
8|load|801|loadbranch|8
9|load|901|loadbranch|9

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2,TRIM=NOTRIM)
INFILE '/data/area.csv' fields terminated BY '|'
INTO TABLE area(area_no,area_name);
```

<span id="inputfileclause" name="inputfileclause" class="yaslink"></span>

### 2. input\_file\_clause

该语句用于指定导入文件的相关信息，可同时指定多个导入文件，以空格分隔。

一个数据加载命令最多可指定250个文件，且同一文件不可重复指定。

<u>导入文件的数据内容格式为</u>：

*列1数据 分隔符 列2数据 分隔符 ...*

其中，每个文件内数据的最大列数为4096，且多个文件的同一位置列代表相同含义，单行数据长度上限为63KB。

<u>数据加载对文件内数据的读取规则为</u>：

- 每一行代表要导入的一条记录。
- 数据列与目标表列字段的映射关系由[column_clause](#columnclause)语句决定。
- 当存在未被映射到的数据列时，该数据列丢弃。
- 当一个数据列以双引号开头时，该双引号将被识别为包围符，此时要求：
  1. 数据列必须以双引号结尾。
  2. 对于中间出现的普通双引号，必须在其前面再加上一个双引号进行转义。
- 当数据列未以双引号开头时，后续所有的双引号都被认为是普通双引号，无需转义。

示例（单机、共享集群部署）

```json
--创建含有JSON字段的customer_intro表
CREATE TABLE customer_intro (id INT, intro JSON);

--在/data目录下创建customer.csv文件，包含如下数据，其中的json数据包含双引号时需转义
1,"[755,false,null,""city"",{""no"":1},[12]]" 
2,""""""
3,"{""city"":""shenzhen"",""no"":2}"
4,"""shenzhen,2"""

--执行加载语句
LOAD DATA 
INFILE '/data/customer.csv'
INTO TABLE customer_intro (id,intro);

SELECT id,intro FROM customer_intro;

          ID INTRO                                                            	
------------ ---------------------------------------------------------------- 	
           1 [755,false,null,"city",{"no":1},[12]]                           
           2 ""                                                              	
           3 {"city":"shenzhen","no":2}                                     	
           4 "shenzhen,2"            
```

#### 2.1. input\_file\_path

指定导入文件的路径及名称，包含如下两种格式：

- 以'/'开头的绝对路径，不支持使用*、？等通配符进行正则匹配。
- 以'?/'开头的相对路径，表示导入文件位于YASDB_DATA目录下。

以上格式中均不可包含'./'和'../'。

指定的导入文件必须存在，且数据库系统用户必须拥有对其的读取权限，否则加载任务报错退出。

可以指定一个空数据文件，不影响加载任务的继续运行。

#### 2.2. fields

指定导入文件的类型，可省略，则默认为CSV文件。

file_type的值只可以为CSV，否则加载任务报错退出。

#### 2.3. with embedded|without embedded

指定换行符是否可被包围符包围作为数据，不指定本语句将默认为WITHOUT EMBEDDED，即换行符不可作为数据。

#### 2.4. fields terminated by

指定导入数据列的分隔符，不指定时默认将逗号作为分隔符。

fields_terminated_char支持三种表示方法：
1. 单字节字符表示（逗号、竖杠号、分号、水平制表符）。
2. 十六进制表示（0x01、0x02、0x03、0x04、0x05、0x06、0x07、0x08、0x0B、0x0C、0x0D、0x0E、0x0F、0x10、0x11、0x12、0x13、0x14、0x15、0x16、0x17、0x18、0x19、0x1A、0x1B、0x1C、0x1D、0x1E、0x1F）。
3. 整数表示（1、2、3、4、5、6、7、8、11、12、13、14、15、16、17、18、19、20、21、22、23、24、25、26、27、28、29、30、31）。

示例（单机、共享集群部署）

```sql
--创建表loadterm
CREATE TABLE loadterm(c1 VARCHAR(10),c2 VARCHAR(10),c3 VARCHAR(10),c4 VARCHAR(10), c5 VARCHAR(10));

--自行创建loadterm.csv，文件位于/data目录下，文件内容为:
8|load|801|loadbranch|8
9|load|901|loadbranch|9

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2)  
INFILE '/data/loadterm.csv' FIELDS csv
WITH EMBEDDED FIELDS TERMINATED BY '|' 
OPTIONALLY ENCLOSED BY '"'
INSERT INTO TABLE loadterm(c1,c2,c3,c4,c5);

--更改loadterm.csv文件内容为：
"1""2""3""4""5"
"6""7""8""9""10"

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2)  
INFILE '/data/loadterm.csv' FIELDS csv
WITH EMBEDDED FIELDS TERMINATED BY 0x01 
OPTIONALLY ENCLOSED BY '"'
INSERT INTO TABLE loadterm(c1,c2,c3,c4,c5);

--更改loadterm.csv文件内容为：
"1"	"2"	"3"	"4"	"5"
"6"	"7"	"8"	"9"	"10"

LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2)  
INFILE '/data/loadterm.csv' FIELDS csv
WITH EMBEDDED FIELDS TERMINATED BY 9 
OPTIONALLY ENCLOSED BY '"'
INSERT INTO TABLE loadterm(c1,c2,c3,c4,c5);
```

#### 2.5. optionally enclosed by

指定导入数据的包围符，只可以指定为双引号，不指定时系统默认为按双引号包围。

示例（单机、共享集群部署）

```sql
LOAD DATA OPTIONS()  
INFILE '/data/area.csv' FIELDS csv
WITH EMBEDDED FIELDS TERMINATED BY '|' 
OPTIONALLY ENCLOSED BY '"'
INSERT INTO TABLE area(area_no,area_name);
```

<span id="badfileclause" name="badfileclause" class="yaslink"></span>

### 3. bad\_file\_clause

该语句用于指定导入文件的存放错误数据的地址，与input_file_clause语句指定的导入文件相关，其中写入错误数据条数受ERRORS参数限制，无论是否指定该语句，只要导入错误数据就会生成错误数据文件，省略则默认在导入文件目录下生成与导入文件同名，后缀为bad的数据文件，存在同名文件时会覆盖该文件。

支持相对路径及绝对路径，支持指定到目录及文件名，若指定到目录，则在指定目录下生成与导入文件同名，后缀为bad的数据文件，存在同名文件时会覆盖该文件。

错误数据不会被导入至数据库中，下述数据会被写入错误数据文件中：

- 类型转换失败的数据。
- 违反约束的数据。
- 不符合CSV格式的数据。
- 未命中分区的数据。

示例（单机、共享集群部署）

```sql
--自行创建badfile.csv和badfile.csv1文件，文件位于/datav目录中，文件内容均为:
"1","2"
"nihao"
"hello"

--创建表bad_load表
CREATE TABLE bad_load(c1 INT,c2 INT);

--执行导入命令
LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=3) 
INFILE '/data/badfile.csv' FIELDS TERMINATED BY ',' 
BADFILE '/data/badfile.bad' 
INSERT INTO TABLE bad_load(c1,c2);

--由于"nihao"和"hello"无法转换成int类型数据，如上两种数据会被记录在错误数据文件中，于终端中执行如下命令查看badfile.bad数据文件内容
$ cat badfile.bad
"nihao"
"hello"

--导入多个数据文件
LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=3) 
INFILE '/data/badfile.csv' FIELDS TERMINATED BY ',' 
BADFILE '/data/badfile.bad' INFILE '/data/badfile.csv1' FIELDS TERMINATED BY ',' 
BADFILE '/data/badfile1.bad' 
INSERT INTO TABLE bad_load(c1,c2);
```

<span id="discardfileclause" name="discardfileclause" class="yaslink"></span>

### 4. discard\_file\_clause

该语句用于指定导入文件的存放过滤数据的地址，与input_file_clause语句指定的导入文件相关，可省略，省略则默认不创建过滤数据文件。如已指定了本语句，但导入数据不满足对应条件，仍不生成过滤数据文件。

支持相对路径及绝对路径，支持指定到目录及文件名，若指定到目录，则在指定目录下生成与导入文件同名，后缀为dsc的数据文件。

过滤数据不会被导入至数据库中，下述数据会被写入过滤数据文件中：

- 不满足when子句的语句。
- 整行映射均为NULL的数据。

### 5. DISCARDS|DISCARDMAX

用于指定数据导入的过滤上限，当数据被过滤的数量达到上限时，终止数据导入，可省略，省略则默认为UINT64_MAX。

如未指定discard_file_clause语句但指定了DISCARDS|DISCARDMAX关键字，将在导入文件目录下生成与导入文件同名，后缀为dsc的数据文件，存在同名文件时会覆盖该文件。

示例（单机、共享集群部署）

```sql
--自行创建discardfile.csv和discardfile1.csv，文件位于/data目录中，文件内容均为:
"3","4"
""
""

--创建表discard_load
CREATE TABLE discard_load(c1 INT,c2 INT);

--执行导入命令
LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=3) 
INFILE '/data/discardfile.csv' FIELDS TERMINATED BY ',' 
DISCARDFILE '/data/discardfile.dsc' 
INSERT INTO TABLE discard_load(c1,c2);

--由于导入文件中的第二行数据均映射为NULL，该行数据会被记录在过滤数据文件中，于终端中执行如下命令查看discardfile.dsc数据文件内容
$ cat discardfile.dsc
""
""

--导入多个数据文件
LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=3) 
INFILE '/data/discardfile.csv' FIELDS TERMINATED BY ',' 
DISCARDFILE '/data/discardfile.dsc' 
INFILE '/data/discardfile1.csv' FIELDS TERMINATED BY ',' 
DISCARDFILE '/data/discardfile1.dsc' 
INSERT INTO TABLE discard_load(c1,c2);
```

<span id="tableclause" name="tableclause" class="yaslink"></span>

### 6. table\_clause

该语句用于指定导入目标表的相关信息，可同时指定多个导入目标表，以空格分隔。

指定多个导入目标表时，这些表必须具有相同的表类型，例如均为HEAP表。

一个数据加载命令最多可指定16个目标表，且同一目标表不可重复指定。

<u>数据加载对导入多个目标表的主要规则为</u>：

- 由于格式错误、映射不匹配等原因导致某一个目标表无法执行导入时，所有的目标表都将无法导入。
- 在所有目标表均可以执行导入时，多个目标表的导入事务独立，即某一个表导入失败并不会导致所有表的导入失败。
- 导入存在外键关联的两个目标表时，应该将父表放在前面。
- 当未明确以COLUMN(csv_column_order)指定目标表列与导入文件数据列的映射关系时，多个目标表按顺序与导入文件的数据列一一映射。

示例（单机、共享集群部署）

```sql
--area表与branches表为两张HEAP表，其中area.area_no为branches.area_no的父键
--自行创建area.csv文件，文件位于/data目录下，文件内容为：
12|load|1201|loadbranch|12
13|load|1301|loadbranch|13

--数据加载命令为：                     
LOAD DATA 
INFILE '/data/area.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches (branch_no,branch_name,area_no);
```

<span id="loadtype" name="loadtype" class="yaslink"></span>

#### 6.1. load\_type

数据加载的执行类型，可省略，则默认为INSERT。

其中，指定为INSERT和APPEND均表示执行对目标表（包括空表和非空表）的插入数据操作。

#### 6.2. table\_name

目标表名称，可带模式指定，需保证当前用户对该表拥有所执行操作的权限。

<span id="whenclause" name="whenclause" class="yaslink"></span>

#### 6.3. when\_clause

对导入的数据指定筛选条件，可指定多个以AND连接的筛选条件。只有满足筛选条件的数据才能被导入。

<span id="loadconditionclause" name="loadconditionclause" class="yaslink"></span>

##### 6.3.1. load\_condition\_clause

筛选条件的内容。

<span id="compareoper" name="compareoper" class="yaslink"></span>

###### 6.3.1.1. compare\_oper

比较符，可以为=、!=、<>。

###### 6.3.1.2. table\_column\_name|csv\_column\_order

可以按目标表列字段或导入文件数据列进行筛选，且允许在多个条件中指定重复的列。

table_column_name需为目标表列字段名称，csv_column_order需为导入文件数据列的列序号。

###### 6.3.1.3. column\_value

以''包围的一个字符串，用于进行列值比较，当为空串（''）时：

- =''的比较结果恒为false
- !=''和<>''的比较结果恒为true

示例（单机、共享集群部署）

```sql
LOAD DATA 
INFILE '/data/area.csv' FIELDS TERMINATED BY '|'
INTO TABLE area WHEN area_no='12' (area_no,area_name)
INTO TABLE branches WHEN (3)='1201' (branch_no,branch_name,area_no);
```

<span id="trailingnullcols" name="trailingnullcols" class="yaslink"></span>

#### 6.4. trailing nullcols

当某个目标表的某个列在导入文件中未映射到数据，但该目标表有其他列能映射到数据（即导入文件中数据列少于目标表的列数）时，指定TRAILING NULLCOLS可以对缺少映射数据的列补NULL值（如果该列上存在一些不允许NULL值的约束定义，补NULL值仍会导致错误），否则该数据会导入失败。

当某个目标表的所有列在导入文件中均未映射到数据（即导入文件中不存在目标表数据）时，指定TRAILING NULLCOLS可以忽略对该表的导入（不影响其他目标表的导入），否则该数据会导入失败。

上述两类错误（指定TRAILING NULLCOLS补NULL值报错和未指定TRAILING NULLCOLS加载任务报错）会影响同一个数据加载命令里的所有目标表，即全部不能导入。

由于存在容错机制，当失败的数据条数未达到容错参数ERRORS指定的容错数据条数上限，语句的执行结果会返回Success。具体的导入情况可查看文件导入的日志文件。

当导入文件中的列数多于目标表的列数时，多余的数据列会被自动忽略且不会抛出错误，这种行为与trailing nullcols配置参数无关。

示例（单机、共享集群部署）

```sql
--自行创建文件area1.csv和area2.csv，文件位于/data目录下
--area1.csv文件内容为：
13|load
--area2.csv文件内容为：
14|load|1401|loadbranch

--不指定TRAILING NULLCOLS，数据导入失败，且所有目标表都不能导入该行数据    
LOAD DATA 
INFILE '/data/area1.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches (branch_no,branch_name,area_no);

--日志（/data/area1.log）内容节选  
infile /data/area1.csv offset 1 is rejected by table BRANCH because table column mismatch csv column.

Table AREA:
  0 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.

Table BRANCHES:
  0 Rows successfully loaded.
  1 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.

--指定TRAILING NULLCOLS加载
--导入area1.csv时将只导入area表数据
LOAD DATA 
INFILE '/data/area1.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches TRAILING NULLCOLS (branch_no,branch_name,area_no);

--日志节选
Table AREA:
  1 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.

Table BRANCHES:
  0 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  1 Rows not loaded because all fields were null.

--导入area2.csv时将导入area表和branches表数据，branches.area_no补NULL
LOAD DATA 
INFILE '/data/area2.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches TRAILING NULLCOLS (branch_no,branch_name,area_no);

--日志节选
Table AREA:
  1 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.

Table BRANCHES:
  1 Rows successfully loaded.
  0 rows not loaded due to data errors.
  0 Rows not loaded because all WHEN clauses were failed.
  0 Rows not loaded because all fields were null.
```

<span id="columnclause" name="columnclause" class="yaslink"></span>

#### 6.5. column\_clause

目标表的列字段，指定多个列字段以`,`分隔。

对于目标表中存在但未在数据加载中指定的列字段，系统自动补NULL值，因此对非空的列字段必须指定，否则数据加载无法成功。

<span id="normalcolumnclause" name="normalcolumnclause" class="yaslink"></span>

##### 6.5.1. normal\_column\_clause

该语句将导入模式指定为普通模式，将导入文件对应列的全部内容用于导入。 

鉴于CSV格式文件存在数据大小限制，如需导入较大的数据文件，建议使用LLS模式进行部分导入或者LOBFILE模式进行全导入 。

###### 6.5.1.1. table\_column\_clause

目标表已存在的列字段名称。

<span id="csvcolumnorder" name="csvcolumnorder" class="yaslink"></span>

###### 6.5.1.2. COLUMN(csv\_column\_order)

对目标表列字段指定到导入文件数据列的映射，<u>可省略，此时处理规则为</u>：

- 当第一个目标表的第一个列字段省略时，默认映射到导入文件的第一个数据列。
- 当非第一个目标表的第一个列字段省略时，默认映射到前一个目标表在导入文件中最后一个映射数据列的下一个数据列，该数据列不存在时按[trailing nullcols](#trailingnullcols)语句规则处理。
- 当非第一个列字段省略时，默认映射到前一个列字段在导入文件中映射数据列的下一个数据列，该数据列不存在时按[trailing nullcols](#trailingnullcols)语句规则处理。

<u>映射未省略的处理规则为</u>：

- 允许多个目标表列字段映射到导入文件的同一个数据列。
- 允许映射到一个不存在的数据列，此时按NULL值导入。

示例（单机、共享集群部署）

```sql
-- 自行准备area3.csv文件，文件位于/data目录下，文件内容为：
15|16|17|18|19

-- 执行加载语句
LOAD DATA 
INFILE '/data/area3.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches TRAILING NULLCOLS (branch_no,branch_name,area_no COLUMN(1));

SELECT area_no,area_name,DHQ FROM area WHERE area_no='15';
AREA_NO AREA_NAME       DHQ           
------- --------------- --------------
15      16              ShenZhen 
SELECT branch_no,branch_name,area_no,address FROM branches WHERE area_no='15';
BRANCH_NO BRANCH_NAME        AREA_NO ADDRESS            
--------- ------------------ ------- -------------------
17        18                 15    
```

<span id="nullifclause" name="nullifclause" class="yaslink"></span>

###### 6.5.1.3. nullif\_clause

本语句用于对列指定是否以NULL值导入的条件，可指定多个以AND连接的条件。当条件结果为true时，数据加载将对该列以NULL值，而非导入文件中映射的数据导入。

**load_condition_clause**

同when_clause中的[load_condition_clause](#loadconditionclause)。

示例（单机、共享集群部署）

```sql
--自行穿件area4.csv文件，文件位于/data目录下，文件内容为：
16|load|1601|loadbranch|16

--执行加载语句，branches.area_no将为NULL而非16
LOAD DATA 
INFILE '/data/area4.csv' FIELDS TERMINATED BY '|'
INTO TABLE area (area_no,area_name)
INTO TABLE branches (branch_no,branch_name,area_no NULLIF (2)='load');

SELECT branch_no,branch_name,area_no,address FROM branches WHERE branch_no='1601';
BRANCH_NO BRANCH_NAME        AREA_NO ADDRESS            
--------- ------------------ ------- -------------------
1601      loadbranch               
```

<span id="fillercolumnclause" name="fillercolumnclause" class="yaslink"></span>

##### 6.5.2. filler\_column\_clause

该语句用于构造伪列，该伪列用于映射CSV文件中的LOB文件地址数据，可被lob_column_clause语句引用。

###### 6.5.2.1. filler\_column\_name

伪列名称，建议与目标表中已存在列字段不同，否则可能导致导入错误。

###### 6.5.2.2. csv\_column\_order

同normal_column_clause中的[csv_column_order](#csvcolumnorder)。

###### 6.5.2.3. nullif\_clause

同normal_column_clause中的[nullif_clause](#nullifclause)。

<span id="lobcolumnclause" name="lobcolumnclause" class="yaslink"></span>

##### 6.5.3. lob\_column\_clause

该语句将导入模式指定为LOBFILE模式，该模式下通过引用FILLER伪列指向的LOB数据文件，将整个文件导入至目标表指定的列中。 

###### 6.5.3.1. table\_column\_name

目标表中已存在的列字段名称。

###### 6.5.3.2. filler\_column\_name

filler_column_clause中指定的伪列名称。

###### 6.5.3.3. terminated\_by\_eof

仅用于语法兼容，无实际含义。

###### 6.5.3.4. nullif\_clause

同normal_column_clause中的[nullif_clause](#nullifclause)。

> **Note**: 
>
> 允许存在未被引用的FILLER伪列，但建议配套使用filler_column_clause和lob_column_clause语句。

示例（单机、共享集群部署）

```sql
--自行创建load_lls.csv文件，文件位于data目录下，内容为：
LOB_FILE.dat.1.2/
LOB_FILE.dat.2.3/

----在数据库中创建表sqlldr_lob
CREATE TABLE sqlldr_lob(c1 INT,c2 CLOB);

LOAD DATA INFILE '/data/load_lls.csv' 
INTO TABLE sqlldr_lob(c1,file1 filler,c2 lobfile(file1) terminated BY eof);
```

<span id="llscolumnclause" name="llscolumnclause" class="yaslink"></span>

##### 6.5.4. lls\_column\_clause

该语句将导入模式指定为LLS（Lob Location Specifier）模式，通过指定LLS关键词选择该导入模式。该模式选取数据文件的部分内容进行导入，且可指定从任意位置和任意长度开始导入。 

指定了LLS关键字的目标表列字段映射的导入文件数据列格式需为` filename.ext.nnn.mmm/ `： 

- filename.ext  为包含LOB数据的文件名称。 
- nnn 是文件中LOB数据的字节的偏移，仅允许为整数，且偏移量不允许超过数据文件大小且不允许为负数。
  -  当输入偏移量的值为正数时，实际偏移量 = 输入值 - 1。 
  - 值为0时，实际偏移量 = 0。
  - 值为负数时返回错误。
-  mmm 是字节中的LOB的长度，仅允许为整数。值为-1时表示null，为0时表示导入一个空LOB，不允许将其指定为小于-1的值。
- 正斜杠（/）为终止字符，格式中必须包含该字符，否则报错。

###### 6.5.4.1. table\_column\_clause

目标表已存在的列字段名称。

###### 6.5.4.2. csv\_column\_order

同normal_column_clause中的[csv_column_order](#csvcolumnorder)。

###### 6.5.4.3. nullif\_clause

同normal_column_clause中的[nullif_clause](#nullifclause)。

示例（单机、共享集群部署）

```sql
--同上例中的文件和表，另外在/data下创建LOB_FILE文件，内容为：
abcdefg
--导入文件列数据为 LOB_FILE.dat.1.2/，意为从LOB_FILE.dat文件的偏移为0的位置为起始，截取长度为2的数据内容进行导入，正斜杠表示终止。
--导入文件列数据为 LOB_FILE.dat.2.3/，意为从LOB_FILE.dat文件的偏移为1的位置为起始，截取长度为3的数据内容进行导入，正斜杠表示终止。

--导入命令为：
LOAD DATA OPTIONS (degree_of_parallelism=3) 
INFILE '/data/load_lls.csv' INTO TABLE sqlldr_lob(c2 LLS);
```

<span id="directoryclause" name="directoryclause" class="yaslink"></span>

#### 6.6. directory\_clause

本语句用于指定对于run_level为split时拆分后的文件存放的目录，由于SPLIT模式仅在yasldr工具中实现，该语句的具体使用见[yasldr使用指导](../../../工具手册/yasldr/yasldr使用指导)章节描述。

不指定时默认以infile的第一个文件所在的目录作为输出目录。
