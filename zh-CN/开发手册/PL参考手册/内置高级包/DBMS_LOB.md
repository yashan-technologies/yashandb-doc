DBMS_LOB包提供了一组内置的函数，用于LOB类型（或可与LOB进行隐式转换的类型）相关的运算。DBMS_LOB包不适用于存算一体分布式集群部署。

## COMPARE

```plsql
DBMS_LOB.COMPARE (
   LOB_1            IN BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW/BFILE,
   LOB_2            IN BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW/BFILE,
   AMOUNT           IN BIGINT DEFAULT MAX(LENGTH(LOB_1), LENGTH(LOB_2)),
   OFFSET_1         IN BIGINT DEFAULT 1,
   OFFSET_2         IN BIGINT DEFAULT 1)
  RETURN INTEGER;
```

COMPARE函数用于比较两个LOB数据的大小，返回一个值为0、-1或者1的INTEGER类型数据。

|  参数| 描述|
| -------- |--------------------------------------------------------------------------------------------------------------------|
| LOB_1    | 用于比较的第一个LOB数据，类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR、RAW或BFILE。                                          |
| LOB_2    | 用于比较的第二个LOB数据，类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR、RAW或BFILE。                                          |
| AMOUNT   | 用于比较的数据长度，函数从偏移位置开始获取该长度的字节数（对于BLOB或RAW）或字符数（对于CLOB、CHAR、VARCHAR、NCLOB、NCHAR或NVARCHAR）进行比较。缺省取LOB_1与LOB_2数据长度的较大值。 |
| OFFSET_1 | LOB_1用于比较的偏移量。缺省为1，即从第1个字节（对于BLOB或RAW）或字符（对于CLOB、CHAR、VARCHAR、NCLOB、NCHAR或NVARCHAR）开始获取AMOUNT长度的数据进行比较。            |
| OFFSET_2 | LOB_2用于比较的偏移量。缺省为1，即从第1个字节（对于BLOB或RAW）或字符（对于CLOB、CHAR、VARCHAR、NCLOB、NCHAR或NVARCHAR）开始获取AMOUNT长度的数据进行比较。            |

本函数的主要规则为：

- 如果LOB_1和LOB_2在OFFSET和AMOUNT参数指定的范围内完全匹配，函数返回0；如果LOB_1小于LOB_2，函数返回-1；如果LOB_1大于LOB_2，函数返回1。
- 当LOB_1和LOB_2中的一个为BLOB或RAW类型，而另一个为CLOB、CHAR、VARCHAR、NCLOB、NCHAR或NVARCHAR类型时，函数返回错误。
- 当LOB_1和LOB_2中的一个为CLOB、CHAR或VARCHAR类型，而另一个为NCLOB、NCHAR或NVARCHAR类型时，函数会将CLOB、CHAR或VARCHAR类型的字符串转码为国家字符集形态，再和NCLOB、NCHAR或NVARCHAR类型进行比较。
- 当LOB_1和LOB_2中的一个为BFILE类型，则另一个必须为BFILE类型时，比较BFILE对应文件的内容数据。
- 出现如下情况之一时，函数返回NULL：
  - AMOUNT < 1
  - OFFSET_1 < 1
  - OFFSET_2 < 1
  - 任一个参数输入了NULL
- AMOUNT、OFFSET_1、OFFSET_2可以为数值型或字符型，函数将隐式转换为BIGINT，该转换对NUMBER类型的小数做截断处理，其他类型的小数按四舍五入处理。
- NCLOB、NCHAR或NVARCHAR类型按国家字符集UTF-16进行编码，固定按2B一个字符进行计算。

示例（HEAP表）

```sql
CREATE TABLE clob_compare(C1 CLOB, C2 CLOB);
INSERT INTO clob_compare VALUES('124SD', '12332');
--对C1、C2进行完全比较
SELECT DBMS_LOB.COMPARE(C1, C2) compare_res FROM clob_compare;
 COMPARE_RES 
------------ 
           1
--比较C1、C2的前两个字符
SELECT DBMS_LOB.COMPARE(C1, C2, 2) compare_res FROM clob_compare;
 COMPARE_RES 
------------ 
           0
--比较C1的第2、3位字符和C2的第5位字符
SELECT DBMS_LOB.COMPARE(C1, C2, 2, 2, 5) compare_res FROM clob_compare;
 COMPARE_RES 
------------ 
           1

CREATE TABLE raw_compare(C1 RAW(10), C2 RAW(10));
INSERT INTO raw_compare VALUES('AB', 'A234');
--对C1、C2进行完全比较
SELECT DBMS_LOB.COMPARE(C1, C2) compare_res FROM raw_compare;
 COMPARE_RES 
------------ 
           1
--比较C1、C2的第一个字节
SELECT DBMS_LOB.COMPARE(C1, C2, 1) compare_res FROM raw_compare;
 COMPARE_RES 
------------ 
           1
--C1未取到数据，而C2获取到第2个字节的比较
SELECT DBMS_LOB.COMPARE(C1, C2, 2, 2, 2) compare_res FROM raw_compare;
 COMPARE_RES 
------------ 
          -1
```

## CONVERTTOBLOB

```plsql
DBMS_LOB.CONVERTTOBLOB(
  dest_lob       IN OUT     BLOB,
  src_clob       IN         CLOB,
  amount         IN         BIGINT,
  dest_offset    IN OUT     BIGINT,
  src_offset     IN OUT     BIGINT, 
  blob_csid      IN         NUMBER,
  lang_context   IN OUT     BIGINT,
  warning        OUT        BIGINT);
```

CONVERTTOBLOB函数用于从源数据CLOB或NCLOB中提取数据，将该数据转换为指定字符集后再以二进制格式写入目标BLOB中。

|  参数| 描述|
| ------- |------------------------------------------------------------------|
| dest_lob | 接收转换结果的目标BLOB，需指定为已存在的BLOB，不能为NULL。 |
| src_clob | 待转换的源数据CLOB或NCLOB，需指定为已存在的CLOB或NCLOB，不能为NULL。 |
| amount | 每次转换的字符数，使用`DBMS_LOB.LOBMAXSIZE`表示转换整个CLOB或NCLOB数据。 |
| dest_offset | * 作为入参时，表示目标BLOB中开始写入的字节偏移量，指定为1表示从起始位置开始。<br />* 作为出参时，表示写入操作结束后目标BLOB中新的字节偏移量。 |
| src_offset | * 作为入参时，表示源数据中开始读取转换的字符偏移量，指定为1表示从起始位置开始。<br />* 作为出参时，表示读取操作结束后源数据中紧跟读取末尾的字符偏移量。 |
| blob_csid | 目标字符集ID，可选取值包括：<br/>852：GBK<br/>871：UTF-8<br/>OCI_UTF16ID：UTF-16<br/>2000：AL16UTF16 |
| lang_context | * 作为入参时，表示当前转换的上下文信息。<br />* 作为出参时，表示转换完成时的上下文信息。 |
| warning      | 警告信息，表示转换过程中发生了异常。目前，唯一可能的警告是不可转换字符，即源字符无法正确转换为目标字符。该信息定义为常量值`DBMS_LOB.WARN_INCONVERTIBLE_CHAR`。                                                            |

示例（单机/共享集群/分布式集群部署）

```plsql
SET serveroutput ON;

DECLARE
  v_dest_blob BLOB;
  v_src_clob CLOB;
  v_amount BIGINT := DBMS_LOB.LOBMAXSIZE;
  v_dest_offset BIGINT := 1;
  v_src_offset BIGINT := 1;
  v_blob_csid BIGINT := 0;
  v_lang_context INTEGER := 0;
  v_warning INTEGER;
  v_clob_data VARCHAR(100) := 'test converttoblob';
BEGIN

-- 创建临时CLOB并填充数据
DBMS_LOB.CREATETEMPORARY(v_src_clob, DBMS_LOB.SESSION);
DBMS_LOB.WRITEAPPEND(v_src_clob, LENGTH(v_clob_data), v_clob_data);
  
-- 创建临时BLOB用于接收转换结果
DBMS_LOB.CREATETEMPORARY(v_dest_blob, DBMS_LOB.CALL);
  
-- 执行转换
DBMS_LOB.CONVERTTOBLOB(
  dest_lob => v_dest_blob,
  src_clob => v_src_clob,
  amount => v_amount,
  dest_offset => v_dest_offset,
  src_offset => v_src_offset,
  blob_csid => v_blob_csid,
  lang_context => v_lang_context,
  warning => v_warning
);
DBMS_OUTPUT.PUT_LINE('CLOB converted to BLOB successfully, contents: ' || RAWTOHEX(DBMS_LOB.SUBSTR(v_dest_blob, DBMS_LOB.GET_LENGTH(v_dest_blob), 1)));
DBMS_LOB.FREETEMPORARY(v_dest_blob);
DBMS_LOB.FREETEMPORARY(v_src_clob);
END;
/
--result
CLOB converted to BLOB successfully, contents: 7465737420636F6E76657274746F626C6F62
```

## CONVERTTOCLOB

```plsql
DBMS_LOB.CONVERTTOCLOB(
  dest_lob       IN OUT     CLOB,
  src_blob       IN         BLOB,
  amount         IN         BIGINT,
  dest_offset    IN OUT     BIGINT,
  src_offset     IN OUT     BIGINT, 
  blob_csid      IN         NUMBER,
  lang_context   IN OUT     BIGINT,
  warning        OUT        BIGINT);
```

CONVERTTOCLOB函数用于从源数据BLOB中提取二进制数据，按指定字符集将其转换为字符数据并写入目标CLOB中。

|  参数| 描述|
| ------- |------------------------------------------------------------------|
| dest_lob | 接收转换结果的目标CLOB，需指定为已存在的CLOB，不能为NULL。  |
| src_blob | 待转换的源数据BLOB，需指定为已存在的BLOB，不能为NULL。 |
| amount | 每次转换的字节数，使用`DBMS_LOB.LOBMAXSIZE`表示转换整个BLOB数据。 |
| dest_offset | * 作为入参时，表示目标CLOB中开始写入操作的字符偏移量，指定为1表示从起始位置开始。<br />* 作为出参时，表示写入操作结束后目标CLOB中新的字符偏移量。 |
| src_offset |* 作为入参时，表示源数据BLOB中开始读取转换的字节偏移量，指定为1表示从起始位置开始。<br />* 作为出参时，表示读取操作结束后源BLOB中紧跟读取末尾的字节偏移量。 |
| blob_csid | 源数据的字符集ID，可选取值包括：<br>0：表示源数据与目标数据的字符集一致<br/>1：ASCII<br/>31：ISO88591<br/>852：GBK<br/>854：GB18030<br/>873：UTF-8<br/>2000：UTF-16 |
| lang_context | * 作为入参时，表示当前转换的上下文信息。<br />* 作为出参时，表示转换完成时的上下文信息。 |
| warning      | 警告信息，表示转换过程中发生了异常。目前，唯一可能出现的警告是不可转换字符，即源字符无法正确转换为目标字符，该信息定义为常量值`DBMS_LOB.WARN_INCONVERTIBLE_CHAR`。                     |


示例（单机/共享集群/分布式集群部署）

```plsql
SET serveroutput ON;

DECLARE
  v_dest_clob CLOB;
  v_src_blob BLOB;
  v_amount BIGINT := DBMS_LOB.LOBMAXSIZE;
  v_dest_offset BIGINT := 1;
  v_src_offset BIGINT := 1;
  v_blob_csid BIGINT := 0;
  v_lang_context INTEGER := 0;
  v_warning INTEGER;
  v_test_data VARCHAR(100) := 'test converttoclob';
  v_raw RAW(32767);
BEGIN

--创建临时BLOB并填充数据
DBMS_LOB.CREATETEMPORARY(v_src_blob);
v_raw := UTL_RAW.CAST_TO_RAW(v_test_data);
DBMS_LOB.WRITEAPPEND(v_src_blob, UTL_RAW.LENGTH(v_raw), v_raw);
  
--创建临时CLOB用于接收转换结果
DBMS_LOB.CREATETEMPORARY(v_dest_clob);
  
--执行转换
DBMS_LOB.CONVERTTOCLOB(
  dest_lob => v_dest_clob,
  src_blob => v_src_blob,
  amount => v_amount,
  dest_offset => v_dest_offset,
  src_offset => v_src_offset,
  blob_csid => v_blob_csid,
  lang_context => v_lang_context,
  warning => v_warning
);
DBMS_OUTPUT.PUT_LINE('BLOB converted to CLOB successfully, contents: ' || v_dest_clob);
DBMS_LOB.FREETEMPORARY(v_dest_clob);
DBMS_LOB.FREETEMPORARY(v_src_blob);
END;
/

--result
BLOB converted to CLOB successfully, contents: test converttoclob
```

<span id="get_length" name="get_length"></span>

## GET_LENGTH

```plsql
DBMS_LOB.GET_LENGTH (
   LOB_LOC    IN  BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW/BFILE) 
RETURN BIGINT;
```

GET_LENGTH函数用于获取指定LOB数据的长度，返回一个BIGINT类型的数值。

|  参数| 描述|
| ------- |------------------------------------------------------------------|
| LOB_LOC | 输入参数，类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR、RAW或BFILE。 |

本函数的主要规则为：

- 输入参数为NULL时，函数返回NULL。
- 输入参数为BLOB、RAW或BFILE类型时，函数返回的是按字节的长度；输入参数为CLOB、CHAR、VARCHAR、NCLOB、NCHAR或NVARCHAR时，函数返回的是按字符的长度。
- NCLOB、NCHAR或NVARCHAR类型按国家字符集UTF-16进行编码，固定按2B一个字符进行计算。

示例（HEAP表）

```sql
CREATE TABLE get_length(C1 CLOB, C2 BLOB);
INSERT INTO get_length VALUES('1U8WQHSD', 'AF1234');
SELECT DBMS_LOB.GET_LENGTH(C1) cl_len,
	   DBMS_LOB.GET_LENGTH(C2) c2_len
FROM get_length;
               CL_LEN                C2_LEN 
--------------------- --------------------- 
                    8                     3
```

## GETLENGTH

同[GET_LENGTH](#get_length)。

<span id="sub_str" name="sub_str"></span>

## SUB_STR

```plsql
DBMS_LOB.SUB_STR (
   LOB_LOC     IN    BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW/BFILE,
   AMOUNT      IN    INTEGER DEFAULT 65534,
   OFFSET      IN    BIGINT DEFAULT 1)
RETURN RAW/VARCHAR2;
```

SUB_STR函数用于读取给定LOB数据的子串，返回RAW或VARCHAR类型的数据。

|  参数| 描述|
|---------|-------------------|
| LOB_LOC | 要读取的LOB数据，类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR、RAW或BFILE。                |
| AMOUNT  | 要读取的字节数（对于BLOB、RAW或BFILE）或字符数（对于CLOB、CHAR、VARCHAR、NCLOB、NCHAR或NVARCHAR），取值范围为[1,65534]，缺省为65534。<br/>* 特例1：NCLOB、NCHAR和NVARCHAR按国家字符集UTF-16进行编码（即固定按2B/字符进行计算），此类数据实际将以32767为最大字符数处理。<br/>* 特例2：为避免AMOUNT = 65534时末尾截断出现残字（报错character set mismatch），系统会自动舍弃末尾的残字。若发生此类舍弃现象，输出长度会小于65534。 |
| OFFSET  | 偏移量。缺省为1，即从第1个字节（对于BLOB、RAW或BFILE）或字符（对于CLOB、CHAR、VARCHAR、NCLOB、NCHAR或NVARCHAR）开始读取AMOUNT长度的数据。       |

本函数的主要规则为：

- 函数对LOB_LOC读取从OFFSET开始的AMOUNT长度的字串，单位为字节（对于BLOB或RAW）或字符（对于CLOB、CHAR、VARCHAR、NCLOB、NCHAR或NVARCHAR）。
- 当LOB_LOC为BLOB、RAW或BFILE类型时，函数返回RAW类型；当LOB_LOC为CLOB、CHAR或VARCHAR类型时，函数返回VARCHAR类型。当LOB_LOC为NCLOB、NCHAR或NVARCHAR类型时，函数返回NVARCHAR类型。
- 出现如下情况之一时，函数返回NULL：
  - AMOUNT < 1
  - AMOUNT > 65534
  - OFFSET < 1
  - 任一个参数输入了NULL

示例（HEAP表）

```sql
CREATE TABLE sub_str(C1 CLOB, C2 BLOB);
INSERT INTO sub_str VALUES('1U8WQHSD', 'AF1234');
--读取全部的C1和C2
SELECT DBMS_LOB.SUB_STR(C1) sub_c1,
	   DBMS_LOB.SUB_STR(C2) sub_c2 
FROM sub_str;
SUB_C1             SUB_C2          
------------------ ------------------
1U8WQHSD           AF1234                                                        

--从C1/C2的第3个字符/字节开始读取2个字符/字节
SELECT DBMS_LOB.SUB_STR(C1, 2, 3) sub_c1,
	   DBMS_LOB.SUB_STR(C2, 2, 3) sub_c2
FROM sub_str;
SUB_C1             SUB_C2          
------------------ ------------------
8W                 34    
```

## SUBSTR

同[SUB_STR](#sub_str)。

## CREATETEMPORARY

```plsql
DBMS_LOB.CREATETEMPORARY (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB,
   cache    IN      BOOLEAN DEFAULT FALSE,
   dur      IN      INTEGER DEFAULT 12);
```

CREATETEMPORARY过程用于创建一个长度为0的临时LOB。

|  参数| 描述|
| :---- | :---- |
| lob_loc | LOB变量，参数的数据类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR或RAW类型。 |
| cache | 保留字段，使用缺省值。BOOLEAN类型或可隐式转换为BOOLEAN类型的参数。不可为NULL。 |
| dur | 保留字段，INTEGER类型或可隐式转换为INTEGER类型的参数的数值范围与INTEGER类型的数值范围相同。可以指定为常量`DBMS_LOB.CALL`或`DBMS_LOB.SESSION`，不可为NULL，默认为12（即`DBMS_LOB.CALL`）。 |

示例（单机/共享集群/分布式集群部署）

```plsql
declare
  clob1 clob;
  len int;
begin
  dbms_lob.createtemporary(clob1);
  len := dbms_lob.getlength(clob1);
  dbms_output.put_line(len);
end;
/
--result
0
```

## FREETEMPORARY

```plsql
DBMS_LOB.FREETEMPORARY (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB);
```

FREETEMPORARY过程用于释放一个临时LOB。临时LOB释放后长度为0，在DBMS_LOB高级包中视为无效的临时LOB。

|  参数| 描述|
| :---- |:---------------------------------------------------------|
| lob_loc | 临时LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB、CLOB或NCLOB类型。不可为NULL。 |

示例（单机/共享集群/分布式集群部署）

```plsql
declare
  clob1 clob;
begin
  dbms_lob.createtemporary(clob1);
  dbms_lob.freetemporary(clob1);
  dbms_output.put_line(length(clob1));
end;
/
--result
0
```

## ISTEMPORARY

```plsql
DBMS_LOB.ISTEMPORARY (
   lob_loc  IN  BLOB/CLOB/NCLOB)
RETURN INTEGER;
```

ISTEMPORARY过程用于判断输入的LOB是否为临时LOB。

- 如果给定的参数是有效的临时LOB，返回1。

- 如果给定的参数是无效的临时LOB或不是临时LOB，返回0。

- 如果给定的参数为NULL，返回NULL。

|  参数| 描述|
| :---- | :---- |
| lob_loc | LOB变量，参数的数据类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR或RAW类型。 |

示例（单机/共享集群/分布式集群部署）

```plsql
create table isnot_templob(id int, c1 clob);
insert into isnot_templob values(1, 'lob');

declare
  clob1 clob;
  istemp int;
begin
  --valid temporary lob
  dbms_lob.createtemporary(clob1);
  istemp := dbms_lob.istemporary(clob1);
  dbms_output.put_line(istemp);
  --invalid temporary lob
  dbms_lob.freetemporary(clob1);
  istemp := dbms_lob.istemporary(clob1);
  dbms_output.put_line(istemp);
  --is not temporary lob
  select c1 into clob1 from isnot_templob where id = 1;
  istemp := dbms_lob.istemporary(clob1);
  dbms_output.put_line(istemp);
end;
/
--result
1
0
0
```

## OPEN

```plsql
DBMS_LOB.OPEN (
   lob_loc    IN OUT  BLOB/CLOB/NCLOB/BFILE,
   open_mode  IN      INTEGER);
```

OPEN过程用于按指定的模式打开一个LOB。

|  参数| 描述|
| :---- |:---------------------------------------|
| lob_loc | 待打开的LOB变量，不可为无效的临时LOB，不可为NULL。参数的数据类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR、RAW或BFILE类型。<br/>当lob_loc为BFILE类型时，同一会话中允许打开的文件数上限受SESSION_MAX_OPEN_FILES参数控制。               |
| open_mode | LOB打开的模式，不能为空。<br/>* DBMS_LOB.LOB_FILEONLY：表示以只读模式打开BFILE。<br/>* DBMS_LOB.LOB_READONLY：表示以只读模式打开LOB。 <br/>* DBMS_LOB.LOB_READWRITE表示以读写模式打开LOB。<br/>若待打开的lob_loc为BFILE，open_mode只能为DBMS_LOB.LOB_READONLY或DBMS_LOB.FILE_READONLY。 |

示例（HEAP表）

```plsql
declare
  clob1 clob := 'read lob';
  amount int := 3;
  buffer varchar(10);
begin
  dbms_lob.open(clob1, dbms_lob.lob_readonly);
  dbms_lob.read(clob1, amount, 6, buffer);
  dbms_output.put_line(buffer);
  dbms_lob.close(clob1);
end;
/
--result
lob
```

## CLOSE

```plsql
DBMS_LOB.CLOSE (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB/BFILE);
```

CLOSE过程用于关闭一个打开的LOB。

|  参数| 描述|
| :---- |:-----------------------------------------------------------------|
| lob_loc | 待关闭的LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB、CLOB、NCLOB或BFILE类型。不可为NULL。 |

示例（HEAP表）

```plsql
declare
  clob1 clob := 'lob';
begin
  dbms_lob.open(clob1, dbms_lob.lob_readwrite);
  dbms_lob.write(clob1, 5, 5, 'write');
  dbms_output.put_line(clob1);
  dbms_lob.close(clob1);
end;
/
--result
lob write
```

## ISOPEN

```plsql
DBMS_LOB.ISOPEN (
   lob_loc  IN  BLOB/CLOB/NCLOB/BFILE) 
RETURN INTEGER; 
```

ISOPEN函数用于判断一个LOB是否处于打开状态。如果LOB处于打开状态，则返回1，否则返回0。

|  参数| 描述|
| :---- |:---------------------------------------------------------------------------------------------|
| lob_loc | LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR、RAW或BFILE类型。不可为NULL。 |

示例（HEAP表）

```plsql
declare
  clob1 clob := 'lob';
  isopen int;
begin
  isopen := dbms_lob.isopen(clob1);
  dbms_output.put_line(isopen);
  dbms_lob.open(clob1, dbms_lob.lob_readwrite);
  isopen := dbms_lob.isopen(clob1);
  dbms_output.put_line(isopen);
  dbms_lob.close(clob1);
  isopen := dbms_lob.isopen(clob1);
  dbms_output.put_line(isopen);
end;
/
--result
0
1
0
```

## READ

```plsql
DBMS_LOB.READ (
   lob_loc  IN      BLOB,
   amount   IN OUT  BIGINT,
   offset   IN      BIGINT,
   buffer   OUT     RAW);

DBMS_LOB.READ (
   lob_loc  IN      CLOB,
   amount   IN OUT  BIGINT,
   offset   IN      BIGINT,
   buffer   OUT     VARCHAR);

DBMS_LOB.READ (
   lob_loc  IN      NCLOB,
   amount   IN OUT  BIGINT,
   offset   IN      BIGINT,
   buffer   OUT     NVARCHAR);
   
 DBMS_LOB.READ (
   lob_loc  IN      BFILE,
   amount   IN OUT  BIGINT,
   offset   IN      BIGINT,
   buffer   OUT     RAW);
```

READ过程用于读取LOB指定部分的数据。

|  参数| 描述|
| :---- |:-------------------------------------------------------------------------------------------------------------------------------------------------|
| lob_loc | 待读取的LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR、RAW或BFILE类型。不可为NULL。                                                 |
| amount | 输入为要读取的BLOB/BFILE字节数或CLOB/NCLOB字符数，至少为1，不能超过65534；输出为实际读取到的BLOB/BFILE字节数或CLOB/NCLOB字符数。           |
| offset | 读取起点在LOB中的偏移量，BLOB或BFILE以字节为单位，CLOB或NCLOB以字符为单位，从1开始，不能超过LOB末尾。不可为NULL。                                                                          |
| buffer | 输出读取到的指定部分LOB数据。<br/>* 当读取BLOB或BFILE时，参数的数据类型可以为RAW、BLOB、CHAR、VARCHAR、NCHAR或NVARCHAR类型。<br/>* 当读取CLOB时，参数的数据类型可以为CLOB、NCLOB、CHAR、VARCHAR、NCHAR、NVARCHAR或RAW类型。 |

示例（单机/共享集群/分布式集群部署）

```plsql
declare
  clob1 clob := 'read lob';
  amount int := 10;
  buffer varchar(10);
begin
  dbms_lob.read(clob1, amount, 6, buffer);
  dbms_output.put_line(buffer);
  dbms_output.put_line(amount);
end;
/
--result
lob
3
```

## WRITE

```plsql
DBMS_LOB.WRITE (
   lob_loc  IN OUT  BLOB,
   amount   IN      BIGINT,
   offset   IN      BIGINT,
   buffer   IN      RAW);

DBMS_LOB.WRITE (
   lob_loc  IN OUT  CLOB,
   amount   IN      BIGINT,
   offset   IN      BIGINT,
   buffer   IN      VARCHAR);

DBMS_LOB.WRITE (
   lob_loc  IN OUT  NCLOB,
   amount   IN      BIGINT,
   offset   IN      BIGINT,
   buffer   IN      NVARCHAR);
```

WRITE过程用于在LOB的指定位置写入指定数量的字节或字符。

|  参数| 描述|
| :---- |:--------------------------------------------------------|
| lob_loc | 待写入的LOB变量，不可为无效的临时LOB。在写入LOB列时需要拥有UPDATE权限。不可为NULL。     |
| amount | 写入BLOB的字节数或写入CLOB/NCLOB的字符数，至少为1，不能超过65534。不可为NULL。     |
| offset | 写入起点在LOB中的偏移量，BLOB以字节为单位，CLOB/NCLOB以字符为单位，从1开始。不可为NULL。 |
| buffer | 待写入LOB的数据，若为LOB类型，不可为无效的临时LOB。不可为NULL。                  |

示例（HEAP表）

```plsql
declare
  clob1 clob := 'lob';
  buffer varchar(10) := 'write lob';
begin
  dbms_lob.write(clob1, 5, 5, buffer);
  dbms_output.put_line(clob1);
end;
/
--result
lob write
```

## APPEND

```plsql
DBMS_LOB.APPEND (
   dest_lob  IN OUT  BLOB, 
   src_lob   IN      BLOB); 

DBMS_LOB.APPEND (
   dest_lob  IN OUT  CLOB, 
   src_lob   IN      CLOB); 

DBMS_LOB.APPEND (
   dest_lob  IN OUT  NCLOB, 
   src_lob   IN      NCLOB); 
```

APPEND过程用于将源LOB的数据附加到目标LOB的末尾。

|  参数| 描述|
| :---- |:----------------|
| dest_lob | 目标LOB变量，不可为无效的临时LOB。在更新LOB列时需要拥有UPDATE权限。参数的数据类型可以为BLOB、RAW、CLOB、NCLOB、CHAR、VARCHAR、NCHAR或NVARCHAR类型。 |
| src_lob | 源LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB、RAW、CLOB、NCLOB、CHAR、VARCHAR、NCHAR或NVARCHAR类型。                       |

示例（HEAP表）

```plsql
declare
  dest_clob clob := 'lob';
  src_clob clob := ' append';
begin
  dbms_lob.append(dest_clob, src_clob);
  dbms_output.put_line(dest_clob);
end;
/
--result
lob append
```

## WRITEAPPEND

```plsql
DBMS_LOB.WRITEAPPEND (
   lob_loc  IN OUT  BLOB, 
   amount   IN      BIGINT, 
   buffer   IN      RAW); 

DBMS_LOB.WRITEAPPEND (
   lob_loc  IN OUT  CLOB, 
   amount   IN      BIGINT, 
   buffer   IN      VARCHAR);

DBMS_LOB.WRITEAPPEND (
   lob_loc  IN OUT  NCLOB, 
   amount   IN      BIGINT, 
   buffer   IN      NVARCHAR);   
```

WRITEAPPEND过程用于将指定数量的字节或字符附加到LOB的末尾。

|  参数| 描述|
| :---- |:-------------------------------------------------------------------------------------------------------|
| lob_loc | 待附加数据的LOB变量，不可为无效的临时LOB。在更新LOB列时需要拥有UPDATE权限。参数的数据类型可以为BLOB、CLOB、VARCHAR、RAW、NCLOB或NVARCHAR类型。不可为NULL。 |
| amount | 附加的BLOB字节数或CLOB/NCLOB字符数，至少为1，不能超过65534。不可为NULL。                                                       |
| buffer | 待附加到LOB末尾的数据，不可为无效的临时LOB。参数的数据类型可以为RAW、BLOB、CHAR、VARCHAR、NCHAR或NVARCHAR类型。                             |

示例（HEAP表）

```plsql
declare
  clob1 clob := 'lob';
  buffer varchar(20) := ' append lob';
begin
  dbms_lob.writeappend(clob1, 7, buffer);
  dbms_output.put_line(clob1);
end;
/
--result
lob append
```

## COPY

```plsql
DBMS_LOB.COPY (
  dest_lob     IN OUT  BLOB,
  src_lob      IN      BLOB,
  amount       IN      BIGINT,
  dest_offset  IN      BIGINT DEFAULT 1,
  src_offset   IN      BIGINT DEFAULT 1);

DBMS_LOB.COPY (
  dest_lob     IN OUT  CLOB,
  src_lob      IN      CLOB,
  amount       IN      BIGINT,
  dest_offset  IN      BIGINT DEFAULT 1,
  src_offset   IN      BIGINT DEFAULT 1);

DBMS_LOB.COPY (
  dest_lob     IN OUT  NCLOB,
  src_lob      IN      NCLOB,
  amount       IN      BIGINT,
  dest_offset  IN      BIGINT DEFAULT 1,
  src_offset   IN      BIGINT DEFAULT 1);
```

COPY过程用于将指定部分的源LOB数据拷贝到目标LOB的指定位置。

|  参数| 描述|
| :---- |:-----------------|
| dest_lob | 目标LOB变量，不可为无效的临时LOB。在更新LOB列时需要拥有UPDATE权限。参数的数据类型可以为BLOB、RAW、CLOB、NCLOB、CHAR、VARCHAR、NCHAR或NVARCHAR类型。 |
| src_lob | 源LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB、RAW、CLOB、NCLOB、CHAR、VARCHAR、NCHAR或NVARCHAR类型。         |
| amount | 拷贝的BLOB字节数或CLOB/NCLOB字符数，至少为1。不可为NULL。                                                                |
| dest_offset | 目标LOB偏移量，BLOB以字节为单位，CLOB/NCLOB以字符为单位，从1开始。不可为NULL。                                                    |
| src_offset | 源LOB偏移量，BLOB以字节为单位，CLOB/NCLOB以字符为单位，从1开始。不可为NULL。                                                     |

示例（HEAP表）

```plsql
declare
  clob1 clob := 'lob';
begin
  dbms_lob.copy(clob1, 'copy lob', 4, 5, 1);
  dbms_output.put_line(clob1);
end;
/
--result
lob copy
```

## ERASE

```plsql
DBMS_LOB.ERASE (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB,
   amount   IN OUT  BIGINT,
   offset   IN      BIGINT DEFAULT 1);
```

ERASE过程用于擦除指定部分的LOB数据。

|  参数| 描述|
| :---- | :---- |
| lob_loc | LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR或RAW类型。在擦除LOB列时需要拥有UPDATE权限。不可为NULL。 |
| amount | 擦除的BLOB字节数或CLOB/NCLOB字符数，至少为1。不可为NULL。 |
| offset | 待擦除的数据在LOB中的偏移量，BLOB以字节为单位，CLOB/NCLOB以字符为单位，从1开始。不可为NULL。 |

示例（HEAP表）

```plsql
declare
  clob1 clob := 'loberaseerase';
  amount int := 5;
begin
  dbms_lob.erase(clob1, amount, 4);
  dbms_output.put_line(clob1);
end;
/
--result
lob     erase
```

## TRIM

```plsql
DBMS_LOB.TRIM (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB/BFILE,
   newlen   IN      BIGINT);
```

TRIM过程用于将LOB修剪至指定长度。

|  参数| 描述|
| :---- |:------------------------------------------------------------------------------------------------------------------|
| lob_loc | LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB、CLOB、CHAR、VARCHAR、NCLOB、NCHAR、NVARCHAR、RAW或BFILE类型。在修剪LOB列时需要拥有UPDATE权限。不可为NULL。 |
| newlen | 修剪后LOB的新长度，BLOB/BFILE以字节为单位，CLOB/NCLOB以字符为单位，至少为0，不能超过LOB的长度。不可为NULL。                                             |

示例（HEAP表）

```plsql
declare
  clob1 clob := 'lob trim newlen';
begin
  dbms_lob.trim(clob1, 8);
  dbms_output.put_line(clob1);
end;
/
--result
lob trim
```

## INSTR

```plsql
DBMS_LOB.INSTR (
   lob_loc  IN  BLOB,
   pattern  IN  RAW,
   offset   IN  BIGINT DEFAULT 1,
   nth      IN  BIGINT DEFAULT 1)
RETURN BIGINT;

DBMS_LOB.INSTR (
   lob_loc  IN  CLOB,
   pattern  IN  VARCHAR,
   offset   IN  BIGINT DEFAULT 1,
   nth      IN  BIGINT DEFAULT 1)
RETURN BIGINT;

DBMS_LOB.INSTR (
   lob_loc  IN  NCLOB,
   pattern  IN  NVARCHAR,
   offset   IN  BIGINT DEFAULT 1,
   nth      IN  BIGINT DEFAULT 1)
RETURN BIGINT;

DBMS_LOB.INSTR (
   lob_loc  IN  BFILE,
   pattern  IN  RAW,
   offset   IN  BIGINT DEFAULT 1,
   nth      IN  BIGINT DEFAULT 1)
RETURN BIGINT;
```

INSTR函数用于返回pattern所表示的子字符串在CLOB、BLOB或NCLOB格式的大对象中第n次出现的位置，匹配时可以从LOB的指定位置开始匹配，如果匹配失败，则返回0。

|  参数| 描述|
| :---- |:----------------------------------------------------------------------------------------------------------------------------------------------------------|
| lob_loc | 待匹配的大对象LOB变量。参数的数据类型可以为BLOB、CLOB、BFILE、CHAR、VARCHAR、RAW、NCLOB、NCHAR或NVARCHAR类型。                        |
| pattern | 待匹配的字符串，长度最大为65534。当匹配BLOB或BFILE时，参数的数据类型可以为RAW、BLOB、CHAR、VARCHAR、NCHAR或NVARCHAR类型。当匹配CLOB或NCLOB时，参数的数据类型可以为RAW、CLOB、NCLOB、CHAR、VARCHAR、NCHAR或NVARCHAR类型。 |
| offset | 匹配起点在LOB中的偏移量，BLOB以字节为单位，CLOB或NCLOB以字符为单位，默认从1开始。                                                                                                         |
| nth | 匹配次数。不设置时默认表示第1次匹配命中的位置。                                                                                                                                  |

示例（单机/共享集群/分布式集群部署）

```plsql
declare
  clob1 clob := 'lob instr lob instr';
  pos bigint;
begin
  pos := dbms_lob.instr(clob1, 'instr', 4, 2);
  dbms_output.put_line(pos);
end;
/
--result
15
```
<span id="dbms_lob_bfile" name="dbms_lob_bfile"></span>

## FILEOPEN

```plsql
DBMS_LOB.FILEOPEN (
   lob_loc     IN OUT  BFILE,
   open_mode   IN      INTEGER);
```

FILEOPEN过程用于只读方式打开BFILE。

同一会话中允许打开的文件数上限受SESSION_MAX_OPEN_FILES参数控制。

|  参数| 描述|
|:----------|:----------------------------------------------------------------------------------|
| lob_loc   | BFILE定位器，不可为NULL。                                                                 |
| open_mode | BFILE打开的模式：支持DBMS_LOB.LOB_READONLY或DBMS_LOB.FILE_READONLY，表示以只读方式打开BFILE，可缺省。 |

示例（HEAP表）

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  lob_loc bfile := BFILENAME('TEST_DIR', 'test.txt');
begin
  dbms_lob.fileopen(lob_loc, 0);
  dbms_output.put_line('file opened');
  dbms_lob.fileclose(lob_loc);
end;
/
--result
file opened
```

## FILEISOPEN

```plsql
DBMS_LOB.FILEISOPEN (
   lob_loc     IN  BFILE);
```

FILEISOPEN方法用于检查BFILE定位器指定的文件是否打开。

如果文件处于打开状态，则返回1，否则返回0。

|  参数| 描述|
|:--------|:------------------|
| lob_loc | BFILE定位器，不可为NULL。 |

示例（HEAP表）

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  lob_loc bfile := BFILENAME('TEST_DIR', 'test.txt');
begin
  DBMS_OUTPUT.PUT_LINE('is file open '||DBMS_LOB.FILEISOPEN(lob_loc));
end;
/
--result
is file open 0
```

## FILEEXISTS

```plsql
DBMS_LOB.FILEEXISTS (
   lob_loc     IN  BFILE);
```

FILEEXISTS方法用于查看BFILE定位器对应的实际文件是否存在。

如果文件存在，则返回1，否则返回0。

|  参数| 描述|
|:--------|:------------------|
| lob_loc | BFILE定位器，不可为NULL。 |

示例（HEAP表）

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  lob_loc bfile := BFILENAME('TEST_DIR', 'test.txt');
begin
   DBMS_OUTPUT.PUT_LINE('is file exist '||DBMS_LOB.FILEEXISTS(lob_loc));
end;
/
--result
is file exist 1
```

## FILEGETNAME

```plsql
DBMS_LOB.FILEGETNAME (
   lob_loc     IN  BFILE,
   dir_alias   OUT VARCHAR
   filename    OUT VARCHAR);
```

FILEGETNAME过程根据BFILE定位器返回目录别名与文件名，并不判断物理文件或目录是否实际存在。

|  参数| 描述|
|:----------|:------------------|
| lob_loc   | BFILE定位器，不可为NULL。 |
| dir_alias | 目录别名，不可为NULL。     |
| filename  | 文件名，不可为NULL。      |

示例（HEAP表）

```plsql
declare
  lob_loc bfile;
  dir_alias varchar(30);
  filename varchar(30);
begin
  lob_loc := bfilename('TEST_DIR', 'test.txt');
  dbms_lob.filegetname(lob_loc, dir_alias, filename);
  dbms_output.put_line('dir_alias is ' || dir_alias);
  dbms_output.put_line('filename is ' || filename);
end;
/
--result
dir_alias is TEST_DIR
filename is test.txt
```

## LOADCLOBFROMFILE

```plsql
DBMS_LOB.LOADCLOBFROMFILE (
   dest_lob     IN OUT  CLOB/NCLOB,
   src_bfile    IN      BFILE,
   amount       IN      BIGINT
   dest_offset  IN OUT  BIGINT,
   src_offset   IN OUT  BIGINT,
   bfile_csid   IN      NUMBER,
   lang_context IN OUT  BIGINT,
   warning      OUT     BIGINT);
```

LOADCLOBFROMFILE过程将BFILE的数据加载到CLOB或NCLOB，加载时进行必要的字符集转换。

|  参数| 描述|
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| dest_lob     | 需要加载到的CLOB或NCLOB，不可为NULL。                                                                                                                                              |
| src_bfile    | 源端BFILE定位器，不可为NULL。                                                                                                                                                    |
| amount       | 从源端BFILE加载的字节数，不可为NULL。使用`DBMS_LOB.LOBMAXSIZE`表示加载到BFILE的末尾。                                                                                                           |
| dest_offset  | 以字符为单位，`（IN）dest_offest`指定目标端CLOB或NCLOB的起始位置开始加载（源点：1），`（OUT）dest_offest`表示本次加载结束后的新的偏移量，也是下次继续加载的起始位置。`dest_offest`始终指向加载结束后第一个完整字符的开头，如果最后一个字符不完整，则偏移量将返回到该不完整字符的开头。 |
| src_offset   | 以字节为单位，`（IN）src_offset`指定源端BFILE的偏移量开始加载（源点：1），`（OUT）src_offset`表示本次加载结束后源端BFILE的字节偏移量，也是下次继续读取的起始位置。                                                                  |
| bfile_csid   | 源端BFILE的字符集，默认为0。对应的取值范围有：<br/>`ASCII：1`<br/>`GBK：852`<br/>`UTF-8：873`<br/>`ISO88591：31`<br/>`UTF-16：2000`<br/>`GB18030：854`                                           |
| lang_context | 当前加载状况的上下文信息，默认为0。该参数仅作兼容适配，对其合法性无特殊校验。                                                                                                                                |
| warning      | 警告信息，表示加载过程中发生了异常。目前，唯一可能的警告是不可转换字符，即源字符无法正确转换为目标字符。该信息定义为常量值`DBMS_LOB.WARN_INCONVERTIBLE_CHAR`。                                                            |

示例（HEAP表）

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  l_clob CLOB;
  l_bfile BFILE;
  l_src_offset BIGINT := 1;
  l_dest_offset BIGINT := 1;
  l_lang_context BIGINT := 0;
  l_warning BIGINT;
begin
  DBMS_LOB.CREATETEMPORARY(l_clob, TRUE);
  l_bfile := BFILENAME('TEST_DIR', 'test.txt');
  DBMS_LOB.FILEOPEN(l_bfile, 0);
  dbms_lob.loadclobfromfile(
      dest_lob    => l_clob,
      src_bfile   => l_bfile,
      amount      => DBMS_LOB.LOBMAXSIZE,
      dest_offset => l_dest_offset,
      src_offset  => l_src_offset,
      bfile_csid  => 0,
      lang_context  => l_lang_context,
      warning       => l_warning
  );
  DBMS_LOB.FILECLOSE(l_bfile);
end;
/
```

## LOADBLOBFROMFILE

```plsql
DBMS_LOB.LOADBLOBFROMFILE (
   dest_lob     IN OUT  BLOB,
   src_bfile    IN      BFILE,
   amount       IN      BIGINT
   dest_offset  IN OUT  BIGINT,
   src_offset   IN OUT  BIGINT)
```

LOADBLOBFROMFILE过程将BFILE的数据加载到BLOB。

|  参数| 描述|
|:------------|:------------------------------------------------------------------------------------------------------|
| dest_lob    | 需要加载到的BLOB，不可为NULL。                                                                                |
| src_bfile   | 源端BFILE定位器，不可为NULL。                                                                                   |
| amount      | 从源端BFILE加载的字节数，不可为NULL。使用`DBMS_LOB.LOBMAXSIZE`表示加载到BFILE的末尾。                                          |
| dest_offset | 以字节为单位，`（IN）dest_offest`指定目标端BLOB的起始位置开始加载（原点：1），`（OUT）dest_offest`表示本次加载结束后的新的偏移量，也是下次继续加载的起始位置。     |
| src_offset  | 以字节为单位，`（IN）src_offset`指定源端BFILE的偏移量开始加载（原点：1），`（OUT）src_offset`表示本次加载结束后源端BFILE的字节偏移量，也是下次继续读取的起始位置。 |

示例（HEAP表）

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  l_blob BLOB;
  l_bfile BFILE;
  l_src_offset BIGINT := 1;
  l_dest_offset BIGINT := 1;
begin
  DBMS_LOB.CREATETEMPORARY(l_blob, TRUE);
  l_bfile := BFILENAME('TEST_DIR', 'test.txt');
  DBMS_LOB.FILEOPEN(l_bfile, 0);
  dbms_lob.loadblobfromfile(
      dest_lob    => l_blob,
      src_bfile   => l_bfile,
      amount      => DBMS_LOB.LOBMAXSIZE,
      dest_offset => l_dest_offset,
      src_offset  => l_src_offset
  );
  DBMS_LOB.FILECLOSE(l_bfile);
end;
/
```

## FILECLOSE

```plsql
DBMS_LOB.FILEOCLOSE (
   lob_loc     IN OUT  BFILE);
```

FILECLOSE过程用于关闭某个已打开的BFILE。

|  参数| 描述|
|:----------|:---------|
| lob_loc   | BFILE定位器，不可为NULL。 |

示例（HEAP表）

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  lob_loc bfile;
begin
  lob_loc := BFILENAME('TEST_DIR', 'test.txt');
  dbms_lob.fileopen(lob_loc);
  dbms_lob.fileclose(lob_loc);
  dbms_output.put_line('file closed');
end;
/
--result
file closed
```

## FILECLOSEALL

```plsql
DBMS_LOB.FILEOCLOSEALL ();
```

FILECLOSEALL过程用于关闭当前session所有打开的BFILE。

示例（HEAP表）

```plsql
begin
  dbms_lob.filecloseall();
  dbms_output.put_line('all file closed');
end;
/
--result
all file closed
```

## 常量定义

|  常量| 类型| 值| 描述|
|:---------------------------------|:--------|:---------|:-------------------------|
| DBMS_LOB.CALL | INTEGER | 12 | 临时LOB在当前调用结束后自动释放（例如存储过程执行完毕） |
| DBMS_LOB.SESSION | INTEGER | 10 | 临时LOB在当前数据库会话结束时释放（例如用户退出会话）<br />仅用于兼容，目前实际表现恒为`DBMS_LOB.CALL` |
| DBMS_LOB.LOBMAXSIZE              | BIGINT  | 2^63 - 1 | LOB的最大大小（以字节为单位）        |
| DBMS_LOB.LOB_READONLY            | INTEGER | 0        | 以只读方式打开指定LOB |
| DBMS_LOB.LOB_READWRITE           | INTEGER | 1        | 以读写方式打开指定LOB      |
| DBMS_LOB.FILE_READONLY           | INTEGER | 0        | 以只读方式打开指定BFILE    |
| DBMS_LOB.DEFAULT_CSID            | INTEGER | 0        | 默认CSID，指定源LOB的字符集为数据库字符集 |
| DBMS_LOB.DEFAULT_LANG_CTX        | INTEGER | 0        | 默认语言上下文信息                |
| DBMS_LOB.NO_WARNING              | INTEGER | 0        | 无警告                      |
| DBMS_LOB.WARN_INCONVERTIBLE_CHAR | INTEGER | 1        | 源端字符集不能正确转换的警告           |

## 异常

|  异常| 描述|
|:---------------|:------------------|
| INVALID_ARGVAL | 参数超出数值范围（除数字溢出外）。 |
| UNOPENED_FILE  | 关闭未打开的LOB。        |

示例（单机/共享集群/分布式集群部署）

```plsql
--invalid_argval exception
declare
  c1 clob:='12345';
begin
  dbms_lob.write(c1,0,1,'6');
exception
  when DBMS_LOB.INVALID_ARGVAL then
    dbms_output.put_line('argument value out of range');
end;
/
--result
argument value out of range

--unopened_file exception
declare
  c1 clob:='12345';
begin
  dbms_lob.close(c1);
exception
  when DBMS_LOB.UNOPENED_FILE then
    dbms_output.put_line('try to close unopened lob');
end;
/
--result
try to close unopened lob
```
