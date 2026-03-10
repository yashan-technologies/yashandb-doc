## yasldr命令行参数

|  参数名称| 取值范围| 参数说明|
|--------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| BATCH_SIZE	              | [1,65535]                         | 控制单个sender线程发送到服务端的最大数据条数，该参数值会影响yasldr的内存资源消耗和执行性能。默认值为4032。                                                                                                                                                                                                                                                |
| CONTROL_FILE	            | -                                 | 包含Load Data DML语句的文件。                                                                                                                                                                                                                                                                                        |
| CONTROL_TEXT	            | -                                 | 包含Load Data DML语句的文本字符串。导入时优先以CONTROL_FILE中的Load Data作为DML控制语句，若未设置CONTROL_FILE则采用CONTROL_TEXT。                                                                                                                                                                                                              |
| MODE	                    | [BASIC,BATCH]                     | 控制导入模式，支持BASIC和BATCH两种导入模式，默认值为BATCH。<br>* BASIC：采用INSERT INTO绑定参数执行，视图、GIS列、XMLTYPE列、interval分区表、存算一体分布式集群部署中的复制表仅支持该模式导入。<br>* BATCH：采用BATCH INSERT协议执行，目前无法导入range interval分区表。                                                                                                                                          |
| PACKET_SIZE<sup>*</sup>	 | [65536,524288]                    | 客户端预期数据包大小（单位：Byte），仅语法兼容，默认值为131072。                                                                                                                                                                                                                                                                        |
| CONN_POOL_SIZE	          | [1,32]                            | 客户端连接池大小，仅语法兼容，默认值为5。                                                                                                                                                                                                                                                                                        |
| SENDERS	                 | [1,128]                           | 控制导入过程中sender线程的数量。若未设置该参数，将按照LOAD DATA语法参数中的DEGREE_OF_PARALELISM和DECODER_THREAD_TIMES参数计算。默认值为1。                                                                                                                                                                                                            |
| CSV_CHUNK_SIZE	          | [16,4096)                         | 控制导入过程中CSV数据文件切分的粒度（单位：MB），默认值为128MB。系统将根据该参数值切分数据文件并将切分后的文件分配给reader线程做进一步解析处理。                                                                                                                                                                                                                             |
| INLINE_BLOB_FORMAT	      | [BINARY,STRING]                   | 控制导入过程中CSV数据文件中BLOB字段的解析方式，默认值BINARY。<br>* BINARY：按照二进制格式进行解析。<br>* STRING：按照字符串形式进行解析。                                                                                                                                                                                                                      |
| PROGRESS	                | [NULL,DETAIL]                     | 控制导入过程中导入进度的展示方式，默认值为NULL。<br>* NULL：导入过程不展示导入进度信息。<br> * DETAIL：导入过程展示CSV文件读取进度百分比，成功导入行数，失败行数，以及空行行数。                                                                                                                                                                                                      |
| LOG_PATH    	            | -                                 | 指定写入日志文件（yasldr.log）的目录，缺省时会在当前执行路径下创建日志文件，支持相对路径。                                                                                                                                                                                                                                                           |
| LOG_LEVEL	               | [OFF,ERROR,WARN,INFO,DEBUG,TRACE] | 控制日志（yasldr.log）的日志级别，默认值为INFO。                                                                                                                                                                                                                                                                              |
| SLICE_POLICY	            | [SEQUENCE,RANDOM]                 | 控制reader线程分配文件切片的算法，默认值为SEQUENCE。<br>* SEQUENCE：顺序分配。<br> * RANDOM：随机分配。                                                                                                                                                                                                                                     |
| DATE_FORMAT              | -                                 | 设置导入时CSV文件中数据类型date对应列的解析格式，默认值为yyyy-mm-dd hh24:mi:ss。                                                                                                                                                                                                                                                       |
| TIME_FORMAT              | -                                 | 设置导入时CSV文件中数据类型time对应列的解析格式，默认值为hh24:mi:ss.ff。                                                                                                                                                                                                                                                               |
| TIMESTAMP_FORMAT         | -                                 | 设置导入时CSV文件中数据类型timestamp对应列的解析格式，默认值为yyyy-mm-dd hh24:mi:ss.ff。                                                                                                                                                                                                                                               |
| TIMESTAMP_TZ_FORMAT      | -                                 | 设置导入时CSV文件中数据类型timestamp with time zone对应列的解析格式，默认值为yyyy-mm-dd hh24:mi:ss.ff tzh:tzm。                                                                                                                                                                                                                        |
| CSV_LINE_SIZE    	       | [1, 1048576]                      | 控制导入过程中单行CSV数据的最大长度（单位：KB），默认值为126KB。                                                                                                                                                                                                                                                                        |
| GIS_SRID	                | [0, INT_MAX]                      | 设置导入GIS数据时的坐标系，默认值为0。                                                                                                                                                                                                                                                                                        |
| CHARACTER_SET            | [ASCII,GBK,UTF8,ISO88591,GB18030] | 设置导入时CSV文件的编码格式，默认与客户端字符集一致。当导入LOB时，此参数同时影响LOB文件的字符集，不支持单独设置LOB文件的字符集。                                                                                                                                                                                                                                       |
| MAX_MEM_SIZE             | [1, 1024]                         | 设置导入时可以使用的最大内存大小（单位：GB），默认值40GB。在分区表导入场景下，导入过程中的内存使用量会随着分区数的增加而迅速增加。为了避免导入过程中出现OOM现象，会在导入前依据分区数进行内存使用预估，计算公式为mem_used = reader_count * 8M + (part_count + reader_count)* 8 * (BATCH_SIZE * (max_line_size * 2 + 8 + 24 + 8 + col_count * 32))，若预估值超过此参数值，则导入启动失败。此时可以尝试增大该参数、或减小BATCH_SIZE参数、或改用BASIC模式进行导入。 |
| BIT_FORMAT               | [DECIMAL, BINARY]                 | 设置导入时CSV文件中数据类型bit对应列的解析方式，支持BINARY和DECIMAL两种数据格式，默认值为BINARY。<br>* DECIMAL：按照10进制格式进行CSV数值解析，此时只允许合法的10进制数。<br>* BINARY：按照二进制格式进行CSV数值解析，此时只允许数字字符0和1。                                                                                                                                                       |
| DELETE	                  | [TRUE,FALSE]                      | 控制导入成功后是否删除原始数据文件，默认值为FALSE。在设置该参数为TRUE前，请谨慎地进行确认。                                                                                                                                                                                                                                                           |
| EMPTY_STRING_AS_NULL	    | [TRUE,FALSE]                      | 设置导入时CSV文件中空字符串的解析方式，默认值为TRUE，表示将空字符串解析为NULL。设置为FALSE后，表示空字符串将解析为非空，一般在MySQL兼容模式下使用。                                                                                                                                                                                                                         |
| SENDER_TIMEOUT	          | [0,86400]                         | 设置导入过程中sender线程的超时退出时间，单位秒，默认值30。若设置为0，则sender线程不会超时退出，此时若表上存在唯一约束，则可能导致导入卡住。                                                                                                                                                                                                                                |
| PART_SEND_POLICY	        | [SQF,HASH,ROUND]                  | 设置导入过程中，数据单元分配给sender发送的算法，默认值为SQF。SQF表示最短队列优先，HASH表示数据单元ID按照sender线程个数进行hash计算。在分区表导入时，采用HASH可显著提升性能。分区表导入时，若CSV数据分布极度不均匀，导致某些分区数据量远远高于其他分区，则需使用SQF算法。                                                                                                                                                      |
| ENABLE_AFFINITY	         | [TRUE,FALSE]                      | 设置导入过程中，reader线程和sender是否进行绑核执行，默认值为TRUE，表示会进行绑核执行。                                                                                                                                                                                                                                                          |
| MOJIBAKE_REPLACE | [TRUE,FALSE] | 导入数据时，是否对数据文件中无法被源数据字符集识别的乱码内容进行容错。默认值为FALSE，表示不对乱码进行容错，若源字节流中存在工具设定字符集不支持的乱码则可能导入失败。<br />乱码容错机制如下：<br />1. 当源字节流中因乱码导致字符集转换失败时，跳过源字节流的1个字节，并在目标缓冲区增加一个`?`字符。<br />2. 若还存在乱码，重复执行上一步直至源字节流结尾。  |

## Load Data DML参数

### 参数列表

|  参数名称| 取值范围| 参数说明|
|------------|---|-------------|
|COMMIT_ROWS	        | [1,UINT32_MAX]        |用来控制提交的粒度。默认值为4096。|
|DECODER_THREAD_TIMES	| [1,255]                |决定线程的分配比。默认值为7。|
|DEGREE_OF_PARALLELISM	| [2,256]                |指定数据加载任务的并发度。默认值为8。|
|ENABLE_BULK	        | [TRUE,FALSE]          |指定是否采用bulk方式导入。默认值为FALSE。|
|ENABLE_DEDUP	        | [TRUE,FALSE]          |指定LSC表的冲突处理方式。默认值为FALSE。|
|ERRORS	                | [0,4294967295]         |指定导入数据的容错上限。默认值为50。|
|LOB_PATH_RELATIVE	    | [TRUE,FALSE]          |指定文件拆分时，行外LOB是否支持绝对路径。默认值为FALSE。|
|LOG	                | -           |指定生成的日志文件。若省略该参数，则默认按照首个导入文件的路径和名称生成.log后缀的日志文件。|
|NOLOGGING	            | [TRUE,FALSE]          |指定是否导入过程中关闭表事务日志。默认值为FALSE。|
|NULL_LOB_FORMAT_SIZE	| [0,1]                 |指定是否支持在LLS导入模式下的数据中NULL值为4个字节的0xFF。默认值为0，即不支持。|
|RUN_LEVEL	            | [SPLIT,SPLIT_TO_PART]|指定程序仅执行拆分文件动作。|
|SILENT	                | [TRUE,FALSE]         |指定导入过程中是否生成记录文件，包括日志文件、错误数据文件及过滤数据文件。默认值为FALSE。|
|STATS	                | [TRUE,FALSE]         |指定导入过程是否记录和生成统计信息报告。默认值为FALSE。该参数仅用于版本兼容，恒为收集并打印统计信息（不受参数值影响）|
|TRIM	                | [LDRTRIM,NOTRIM]     |指定是否修剪处理CSV数据文件中的空格。默认值为LDRTRIM。表示修剪数据列起始的空格、制表符。|

### 详细说明

#### COMMIT\_ROWS

决定事务的提交频率，假设其值为n，解码数据文件中n条数据，提交一次事务，使用BULKLOAD导入时，该参数无效。

parameter_value须为一个范围在[1,4294967295]之间的整数，默认值为4096。

#### DECODER\_THREAD\_TIMES

决定线程的分配比，即决定READER线程与SENDER线程的比值，两种线程的总和不超过DEGREE_OF_PARALLELISM。

在未设置SENDERS参数时，SENDER_CNT=round(DOP, DECODER_THREAD_TIMES + 1), READER_CNT=MIN(SLICE_CNT, DOP - SENDER_CNT)。

parameter_value须为一个范围在[1,255]之间的整数，默认值为7。

#### DEGREE\_OF\_PARALLELISM

指定数据加载任务的并行度，受执行机器上CPU核数的限制，最大为CPU核数的4倍。

parameter_value须为一个范围在[2,256]之间的整数，默认值为8，该数值表示用户期望的并行度值，系统会结合用户期望值、硬件环境、数据文件大小等计算实际的并行度值，并使加载任务按实际值并发运行。

> **Note**:
>
> 若导入语句中指定WITH EMBEDDED选项，且DEGREE_OF_PARALLELISM设置在[3,20]之间，则为了提升导入性能，yasldr会单独启动一个FileReader线程，用来处理文件内容的流水线读取。

#### ENABLE\_BULK

当其值为TRUE时，yasldr工具使用bulkload方式导入，仅支持LSC表。

当导入复制表时，yasldr会使用basic模式导入，此时ENABLE_BULK标识不生效。

parameter_value须为TRUE或FALSE，默认值为FALSE。

#### ENABLE\_DEDUP

指定LSC表的冲突处理方式。

该选项必须与ENABLE_BULK选项同时使用，且yasldr需要工作在BATCH导入模式下，parameter_value须为TRUE或FALSE，默认值为FALSE。

当其值为TRUE时，LSC表对导入过程中产生唯一约束冲突的数据会进行去重处理，导入完成后显示的成功数为原始的数据记录数，表中的实际记录数需要手动查询确认。

#### ERRORS

指定数据导入的容错数据条数上限，当数据错误条数达到上限时，终止数据导入。多个文件导入时，错误数据条数累加计算。

parameter_value须为一个范围在[0,4294967295]之间的整数，默认值为50。

#### LOB\_PATH\_RELATIVE

指定进行文件拆分时，outline lob导入形式的LOB文件路径类型。

parameter_value须为TRUE或FALSE，默认值为FALSE。

- 如果指定为TRUE，则进行文件拆分时，仅支持LOB文件指定为相对路径。若指定为绝对路径，执行语句会报错。
- 如果指定为FALSE，则进行文件拆分时，LOB文件可以指定为相对路径或绝对路径。若指定为绝对路径，yasboot不会将数据拷贝到远端节点。

#### LOG

指定生成日志文件，记录执行数据加载任务的过程中过程信息，包括数据导入失败原因，导入成功条数等信息，与input_file_clause语句指定的导入文件相关。无论是否指定本参数，均会生成日志文件，省略则默认在首个导入文件目录下生成与该文件同名，后缀为log的日志文件。

parameter_value支持相对路径及绝对路径，仅支持指定到文件名。

> **Note**:
>
> YashanDB数据导入生成日志文件的相关参数有SILENT、STATS和LOG，三种参数的关系如下：
>
> - SILENT为TRUE时，STATS和LOG参数失效，此时不会生成导入日志、错误数据文件和过滤数据文件。
> - SILENT为FALSE时：STATS参数为TRUE或FALSE，日志文件内均包含统计信息。

#### NOLOGGING

当其值为TRUE，对于语句中声明的状态为LOGGING的表，会将其转为NOLOGGING进行导入，导入结束后，将恢复表的LOGGING状态；当其值为FALSE，不对表的LOGGING状态做操作。

若表的初始状态为LOGGING，导入完成后需恢复表的LOGGING状态，机制为异步，在后台进行表的LOGGING状态转换，该行为可能失败，转换结果通过运行日志记载，详情参见LOGGING ASYNC动作。

parameter_value需为TRUE或FALSE，默认值为FALSE。

只建议在数据迁移场景设置nologging属性，且需要关注如下事项：

- 主备环境不可启用nologging，需要在完成数据导入后再建备库。
- 使用nologging导入后，执行一次全量checkpoint可以保证数据持久性，否则宕机后可能导致数据丢失。
- 当表的属性为nologging，同时发生宕机时，重启后该表将被置为corrupted，只能通过truncate表进行恢复，且需要重新执行上述导入过程以恢复数据。
- 如果一个事务失败，该事务内所有执行过插入数据操作的nologging表都会被标记为corrupted。
- nologging属性的LSC表使用bulkload模式导入数据时，不受nologging属性影响（性能无变化，失败也不会被标记为corrupted）。
- 当表为nologging时，不能对其执行update和delete操作，导入操作完成后需及时将其修改为logging。
- 设置表状态为nologging属于DDL操作，会加表锁。如果并发执行DML语句，可能导致DML语句失败。
- 当表为nologging，或使用nologging方式导入时，在违反约束条件时可能出现容错失败的现象，具体受违反约束的数据在所有数据中的位置影响。

##### NULL\_LOB\_FORMAT\_SIZE

值为1，表示特定的LOB类型NULL值表示，该模式下会组织成4B全F，只在LLS导入适配下使用；值为0，表示正常的LOB类型NULL值表示，该模式下会组织成8B全F，是一种通用的LOB类型NULL表示方式。

parameter_value只有0或1，其他值无效，默认值为0。

##### RUN\_LEVEL

本选项用于指定程序执行的任务类型，设置后可指定任务类型为文件拆分任务；不设置时默认为数据导入任务，不会进行文件拆分操作。

**SPLIT模式**

本模式用于执行CSV文件的拆分任务，不执行数据的导入。

大数据量情况下（超过500G），建议先执行此命令将CSV文件进行拆分后再导入。

> **Note**: 
>
> 复制表不支持连接CN后进行拆分，但支持连接DN后按照分区进行拆分。
> YashanDB内置的yasboot工具可实现一键拆分和导入CSV文件的功能，详情请参考[数据导入命令](../yasboot/yasboot命令介绍/yasboot load)章节。

CSV文件将按照分区键进行拆分，指定directory时，拆分后的文件生成路径为指定路径，未指定directory时，拆分后的文件生成路径为INFILE的第一个文件所在目录。

示例

```bash
# area.csv文件内容为:
6|load|601|loadbranch|6
7|load|701|loadbranch|7

$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA OPTIONS(RUN_LEVEL=SPLIT,DEGREE_OF_PARALLELISM=2) INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq) directory '/home/yasdb/file''"
```

**SPLIT_TO_PART模式**

本模式用于按照节点分区执行CSV文件的拆分任务，不执行数据的导入。

文件生成路径：

- 单机部署中，生成路径同SPLIT模式。
- 存算一体分布式集群部署中拆分对象为非分区表时，生成路径同SPLIT模式。
- 存算一体分布式集群部署中拆分对象为多分区表时，将在directory指定路径或INFILE的第一个文件所在目录下生成以`节点IP:端口号`命名的文件夹，并在该文件夹中按照分区生成拆分后的文件。

#### SILENT

指定导入过程中是否生成记录文件，包括日志文件、错误数据文件及过滤数据文件。当其值为TRUE，表示不生成记录文件；值为FALSE，表示生成记录文件。

parameter_value须为TRUE或FALSE，默认值为FALSE。

#### STATS

指定是否打印导入过程中的统计信息。该参数仅用来实现版本兼容，不论是否指定该参数，均会统计收集并打印。打印输出信息的日志会保存至导入数据文件所在的路径中，且与该数据文件同名，文件格式为log格式。

parameter_value须为TRUE或FALSE。

具体打印的统计信息如下：

- CONTROLLER统计信息：展示READER和SENDER线程的数量、解析耗时和准备执行环境的耗时。
- READER统计信息：展示IO次数、IO耗时、解码的行数、行最大宽度、行平均宽度、读取行的总耗时、解码总耗时、分区计算总耗时、转换总耗时、等待分区可用总耗时、发送队列总耗时以及线程的总运行耗时。
- SENDER统计信息：展示发送次数、发送总耗时、单次最大发送耗时、执行提交的次数、提交的总耗时、获取发送单元的总耗时、转换总耗时以及线程总运行耗时。
- NODE统计信息（仅在存算一体分布式集群部署中存在）：发送到节点的行数、发送的次数、发送总耗时、单次最大发送耗时、提交次数、提交总耗时和节点的地址信息

输出的统计信息具体描述见[yasldr使用指导](yasldr使用指导)中的统计信息。

#### TRIM

表示对CSV数据文件中空格的处理情况。LDRTRIM表示修剪数据列起始的空格、制表符，NOTRIM表示不对数据列起始的空格、制表符做处理。对于有包围符的数据，包围符内的字符均视为数据，不受该参数约束。

parameter_value须为LDRTRIM或NOTRIM，默认值为LDRTRIM。
