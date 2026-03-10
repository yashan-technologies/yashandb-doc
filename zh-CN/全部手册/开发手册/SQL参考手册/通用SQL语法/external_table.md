
**[external\_table\_clause](#externaltableclause)::=**

```ebnf+diagram
syntax::=  "(" [TYPE access_driver_type] [external_table_data_props_clause] ")" [REJECT LIMIT (integer|UNLIMITED)]
```

**[external\_table\_data\_props\_clause](#externaltabledatapropsclause)::=**

```ebnf+diagram
syntax::=  [DEFAULT DIRECTORY directory_name] [ACCESS PARAMETERS "(" [record_format_info_clause] [field_definitions_clause] ")"] [LOCATION "(" [directory_name ":"] "'" location_specifier "'" ")"]
```

**[record\_format\_info\_clause](#recordformatinfoclause)::=**

```ebnf+diagram
syntax::=  RECORDS ( (DELIMITED BY NEWLINE) | (output_files_clause)) {"" ((DELIMITED BY NEWLINE) | (output_files_clause))}
```

**[output\_files\_clause](#outputfilesclause)::=**

```ebnf+diagram
syntax::=  ( (NOBADFILE | BADFILE file_pos_clause) | (NOLOGFILE | LOGFILE file_pos_clause) ) {"" ( (NOBADFILE | BADFILE file_pos_clause) | (NOLOGFILE | LOGFILE file_pos_clause) )}
```

**[file\_pos\_clause](#fileposclause)::=**

```ebnf+diagram
syntax::=  [directory_name ":"]  "'" file_name "'"
```

**[field\_definitions\_clause](#fielddefinitionsclause)::=**

```ebnf+diagram
syntax::=  FIELDS ( (TERMINATED BY "'"char"'") | (OPTIONALLY ENCLOSED BY "'"char"'") ) {"" ( (TERMINATED BY "'"char"'") | (OPTIONALLY ENCLOSED BY "'"char"'") )}
syntax::=  FIELDS ( (TERMINATED BY ("'"char"'" | ("0x" | "x") "'" "9" "'")) | (OPTIONALLY ENCLOSED BY "'"char"'") ) {"" ( ((TERMINATED BY ("'"char"'" | ("0x" | "x") "'" "9" "'")) | (OPTIONALLY ENCLOSED BY "'"char"'")) )}
```

<span id="externaltableclause" name="externaltableclause" class="yaslink"></span>

external_table_clause用于在创建一个外部表时，指定其对应的外部数据信息。

本语句不会对外部数据是否存在及正确进行校验，即只要语法正确均可创建成功。查询一个外部表时，系统根据本语句所定义的外部数据信息进行检索，不满足条件时则会报错。

### TYPE

通过TYPE关键字指定外部表驱动名，可省略。access_driver_type的值可为YASDB_LOADER或ORACLE_LOADER，二者等价。

<span id="externaltabledatapropsclause" name="externaltabledatapropsclause" class="yaslink"></span>

### external\_table\_data\_props\_clause

指定外部表中数据的属性，可省略。

#### DEFAULT DIRECTORY

指定外部表对应数据文件所在的缺省目录，可省略。当后续语句中未指定directory_name时，将以本目录作为数据或日志文件所在的默认目录。



directory_name必须为YashanDB中已创建的一个目录对象，创建方式参考[CREATE DIRECTORY](../SQL语句/CREATE DIRECTORY)。



#### ACCESS PARAMETERS

指定在查询外部表时，读取外部数据文件的参数，包括源文件名称、列格式以及日志记录。

<span id="recordformatinfoclause" name="recordformatinfoclause" class="yaslink"></span>

#### record\_format\_info\_clause

指定数据行格式，可省略。

##### DELIMITED BY NEWLINE

指定读取外部数据文件时，数据记录之间的分隔符是换行符。

<span id="outputfilesclause" name="outputfilesclause" class="yaslink"></span>

##### output\_files\_clause

指定数据库在读取外部数据时的日志信息，若省略该子句，系统将以默认名称创建日志文件进行记录。

###### NOBADFILE | BADFILE

指定BADFILE时，查询外部数据过程中遇到的错误数据将记录在file_pos_clause指定的文件中。

指定NOBADFILE时，系统不会记录查询外部数据时的错误数据。

###### NOLOGFILE | LOGFILE

指定LOGFILE时，查询外部数据的操作过程将记录在file_pos_clause指定的文件中。

指定NOLOGFILE时，系统不会记录查询外部数据时的操作信息。

<span id="fileposclause" name="fileposclause" class="yaslink"></span>

###### file\_pos\_clause

指定上述badfile或logfile所对应的物理文件，格式为directory_name:file_name。

directory_name必须为YashanDB中已创建的一个目录对象，省略时默认为DEFAULT DIRECTORY所指定目录对象。

>**Note**:
>
> 出于性能考虑，在共享集群/分布式集群部署中**不建议**将badfile和logfile的directory设置在YFS。

file_name为一个操作系统可接受的文件名称，对于不存在的文件将由系统在记录日志时创建，对于已存在的文件，系统将对badfile进行覆盖填写，对logfile进行追加填写。

对于file_name，数据库会对部分符号进行替换：

- `%%`将被替换为`%`；

- `%a`、`%p`将被替换为当前线程ID。

<span id="fielddefinitionsclause" name="fielddefinitionsclause" class="yaslink"></span>

#### field\_definitions\_clause

指定外部数据列格式，可省略。

##### TERMINATED BY

指定外部数据文件的字段之间的分隔符，分隔符的类型只能为字符，0x'9'、x'9'被解析为制表符，应与外部数据文件中实际使用的分隔符保持一致，若省略则默认为','。

##### OPTIONALLY ENCLOSED BY

如果外部数据文件中使用了封闭符号（只能使用'"'封闭符），需通过本语句进行指定，且只能指定为'"'。

#### LOCATION

指定外部数据文件所在的路径，格式为(directory_name:location_specifier)。

directory_name必须为YashanDB中已创建的一个目录对象，省略时默认为DEFAULT DIRECTORY所指定目录对象。

location_specifier为一个已存在的CSV文件的名称。

### REJECT LIMIT

指定在查询外部数据时，可容忍的最多错误数据行数，达到容忍值时系统将终止查询操作。可省略，则默认为0行。UNLIMITED表示容许所有的数据错误。

示例

```sql
-- 在/data/area.csv中存在如下数据：
8|load|801|loadbranch|8
9|load|901|loadbranch|9

-- 在数据库中创建目录对象和外部表
CREATE DIRECTORY dir_ext AS '/data';
CREATE TABLE area_external(
 area_no CHAR(2),
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen')
ORGANIZATION EXTERNAL(
 TYPE YASDB_LOADER
 DEFAULT DIRECTORY dir_ext
 ACCESS PARAMETERS(
  RECORDS BADFILE 'area_bad.log' LOGFILE 'area_log.log'
  FIELDS TERMINATED BY ',')
 LOCATION ('area.csv')
)
REJECT LIMIT 1;

-- 查询表数据，由于文件内数据格式与表定义格式不匹配，将无法查询到数据且达到容错行数退出
SELECT * FROM area_external;
YAS-02687 reject limit reached
```
