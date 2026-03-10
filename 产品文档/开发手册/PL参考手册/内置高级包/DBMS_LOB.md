DBMS_LOB包提供了一组内置的函数，用于LOB类型（或可与LOB进行隐式转换的类型）相关的运算。DBMS_LOB包不适用于分布式部署。

## COMPARE

```plsql
DBMS_LOB.COMPARE (
   LOB_1            IN BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW,
   LOB_2            IN BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW,
   AMOUNT           IN BIGINT DEFAULT MAX(LENGTH(LOB_1), LENGTH(LOB_2)),
   OFFSET_1         IN BIGINT DEFAULT 1,
   OFFSET_2         IN BIGINT DEFAULT 1)
  RETURN INTEGER;
```

COMPARE函数用于比较两个LOB数据的大小，返回一个值为0、-1或者1的INTEGER类型数据。

| 参数     | 描述                                                        |
| -------- | ------------------------------------------------------------ |
| LOB_1    | 用于比较的第一个LOB数据，类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW。 |
| LOB_2    | 用于比较的第二个LOB数据，类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW。 |
| AMOUNT   | 用于比较的数据长度，函数从偏移位置开始获取该长度的字节数（对于BLOB/RAW）或字符数（对于CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR）进行比较。缺省取LOB_1与LOB_2数据长度的较大值。 |
| OFFSET_1 | LOB_1用于比较的偏移量。缺省为1，即从第1个字节（对于BLOB/RAW）或字符（对于CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR）开始获取AMOUNT长度的数据进行比较。 |
| OFFSET_2 | LOB_2用于比较的偏移量。缺省为1，即从第1个字节（对于BLOB/RAW）或字符（对于CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR）开始获取AMOUNT长度的数据进行比较。 |

本函数的主要规则为：

- 如果LOB_1和LOB_2在OFFSET和AMOUNT参数指定的范围内完全匹配，函数返回0；如果LOB_1小于LOB_2，函数返回-1；如果LOB_1大于LOB_2，函数返回1。
- 当LOB_1和LOB_2中的一个为BLOB/RAW类型，而另一个为CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR类型时，函数返回错误。
- 当LOB_1和LOB_2中的一个为CLOB/CHAR/VARCHAR类型，而另一个为NCLOB/NCHAR/NVARCHAR类型时，函数会将CLOB/CHAR/VARCHAR类型的字符串转码为国家字符集形态，再和NCLOB/NCHAR/NVARCHAR类型进行比较。
- 出现如下情况之一时，函数返回NULL：
  - AMOUNT < 1
  - OFFSET_1 < 1
  - OFFSET_2 < 1
  - 任一个参数输入了NULL
- AMOUNT、OFFSET_1、OFFSET_2可以为数值型或字符型，函数将隐式转换为BIGINT，该转换对NUMBER类型的小数做截断处理，其他类型的小数按四舍五入处理。
- NCLOB/NCHAR/NVARCHAR按国家字符集UTF-16进行编码，固定按2B一个字符进行计算。

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

## GET\_LENGTH

```plsql
DBMS_LOB.GET_LENGTH (
   LOB_LOC    IN  BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW) 
RETURN BIGINT;
```

GET_LENGTH函数用于获取指定LOB数据的长度，返回一个BIGINT类型的数值。

| 参数    | 描述                                                      |
| ------- | ------------------------------------------------------------ |
| LOB_LOC | 输入参数，类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW。 |

本函数的主要规则为：

- 输入参数为NULL时，函数返回NULL。
- 输入参数为BLOB/RAW类型时，函数返回的是按字节的长度；输入参数为CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR时，函数返回的是按字符的长度。
- NCLOB/NCHAR/NVARCHAR按国家字符集UTF-16进行编码，固定按2B一个字符进行计算。

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
同GET_LENGTH。

## SUB\_STR

```plsql
DBMS_LOB.SUB_STR (
   LOB_LOC     IN    BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW,
   AMOUNT      IN    INTEGER DEFAULT 65534,
   OFFSET      IN    BIGINT DEFAULT 1)
RETURN RAW/VARCHAR2;
```

SUB_STR函数用于读取给定LOB数据的子串，返回RAW或者VARCHAR类型的数据。

| 参数      | 描述                                                                                                                                                                                                                                                                                  |
|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LOB_LOC | 要读取的LOB数据，类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW。                                                                                                                                                                                                                     |
| AMOUNT  | 要读取的字节数（对于BLOB/RAW）或字符数（对于CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR），取值范围为[1,65534]，缺省为65534。<br/>* 特例1：NCLOB/NCHAR/NVARCHAR按国家字符集UTF-16进行编码（即固定按2B/字符进行计算），此类数据实际将以32767为最大字符数处理。<br/>* 特例2：为避免AMOUNT = 65534时末尾截断出现残字（报错character set mismatch），系统会自动舍弃末尾的残字。若发生此类舍弃现象，输出长度会小于65534。 |
| OFFSET  | 偏移量。缺省为1，即从第1个字节（对于BLOB/RAW）或字符（对于CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR）开始读取AMOUNT长度的数据。                                                                                                                                                                                           |

本函数的主要规则为：

- 函数对LOB_LOC读取从OFFSET开始的AMOUNT长度的字串，单位为字节（对于BLOB/RAW）或字符（对于CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR）。
- 当LOB_LOC为BLOB/RAW类型时，函数返回RAW类型；当LOB_LOC为CLOB/CHAR/VARCHAR类型时，函数返回VARCHAR类型。当LOB_LOC为NCLOB/NCHAR/NVARCHAR类型时，函数返回NVARCHAR类型。
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
同SUB_STR。

## CREATETEMPORARY

```plsql
DBMS_LOB.CREATETEMPORARY (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB,
   cache    IN      BOOLEAN DEFAULT FALSE,
   dur      IN      INTEGER DEFAULT 10);
```

CREATETEMPORARY过程用于创建一个长度为0的临时LOB。

| 参数 | 描述 |
| :---- | :---- |
| lob_loc | LOB变量，参数的数据类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW类型。 |
| cache | 保留字段，使用缺省值。BOOLEAN类型或可隐式转换为BOOLEAN类型的参数。不可为NULL。 |
| dur | 保留字段，使用缺省值。INTEGER类型或可隐式转换为INTEGER类型的参数的数值范围与INTEGER类型的数值范围相同。不可为NULL。 |

示例（单机、共享集群部署）

```plsql
DECLARE
  clob1 CLOB;
  len INT;
BEGIN
  dbms_lob.createtemporary(clob1);
  len := dbms_lob.getlength(clob1);
  dbms_output.put_line(len);
END;
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

| 参数 | 描述                                                       |
| :---- |:---------------------------------------------------------|
| lob_loc | 临时LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB/CLOB/NCLOB类型。不可为NULL。 |

示例（单机、共享集群部署）

```plsql
DECLARE
  clob1 CLOB;
BEGIN
  dbms_lob.createtemporary(clob1);
  dbms_lob.freetemporary(clob1);
  dbms_output.put_line(LENGTH(clob1));
END;
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
如果给定的参数是有效的临时LOB，则返回1。
如果给定的参数是无效的临时LOB或者不是临时LOB，则返回0。
如果给定的参数为NULL，则返回NULL。

| 参数 | 描述 |
| :---- | :---- |
| lob_loc | LOB变量，参数的数据类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW类型。 |

示例（单机、共享集群部署）

```plsql
CREATE TABLE isnot_templob(id INT, c1 CLOB);
INSERT INTO isnot_templob VALUES(1, 'lob');

DECLARE
  clob1 CLOB;
  istemp INT;
BEGIN
  --valid TEMPORARY LOB
  dbms_lob.createtemporary(clob1);
  istemp := dbms_lob.istemporary(clob1);
  dbms_output.put_line(istemp);
  --invalid TEMPORARY LOB
  dbms_lob.freetemporary(clob1);
  istemp := dbms_lob.istemporary(clob1);
  dbms_output.put_line(istemp);
  --IS NOT TEMPORARY LOB
  SELECT c1 INTO clob1 FROM isnot_templob WHERE id = 1;
  istemp := dbms_lob.istemporary(clob1);
  dbms_output.put_line(istemp);
END;
/
--result
1
0
0
```

## OPEN

```plsql
DBMS_LOB.OPEN (
   lob_loc    IN OUT  BLOB/CLOB/NCLOB,
   open_mode  IN      INTEGER);
```

OPEN过程用于按指定的模式打开一个LOB。

| 参数 | 描述                                                   |
| :---- |:-----------------------------------------------------|
| lob_loc | 待打开的LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW类型。不可为NULL。 |
| open_mode | LOB打开的模式。输入DBMS_LOB.LOB_READONLY，以只读模式打开LOB；输入DBMS_LOB.LOB_READWRITE，以读写模式打开LOB。不可为NULL。   |

示例（HEAP表）

```plsql
DECLARE
  clob1 CLOB := 'read lob';
  amount INT := 3;
  buffer VARCHAR(10);
BEGIN
  dbms_lob.OPEN(clob1, dbms_lob.lob_readonly);
  dbms_lob.read(clob1, amount, 6, buffer);
  dbms_output.put_line(buffer);
  dbms_lob.CLOSE(clob1);
END;
/
--result
lob
```

## CLOSE

```plsql
DBMS_LOB.CLOSE (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB);
```

CLOSE过程用于关闭一个打开的LOB。

| 参数 | 描述                                                        |
| :---- |:----------------------------------------------------------|
| lob_loc | 待关闭的LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB/CLOB/NCLOB类型。不可为NULL。 |

示例（HEAP表）

```plsql
DECLARE
  clob1 CLOB := 'lob';
BEGIN
  dbms_lob.OPEN(clob1, dbms_lob.lob_readwrite);
  dbms_lob.write(clob1, 5, 5, 'write');
  dbms_output.put_line(clob1);
  dbms_lob.CLOSE(clob1);
END;
/
--result
lob write
```

## ISOPEN

```plsql
DBMS_LOB.ISOPEN (
   lob_loc  IN  BLOB/CLOB/NCLOB) 
RETURN INTEGER; 
```

ISOPEN函数用于判断一个LOB是否处于打开状态。如果LOB处于打开状态，则返回1，否则返回0。

| 参数 | 描述                                                                       |
| :---- |:-------------------------------------------------------------------------|
| lob_loc | LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW类型。不可为NULL。 |

示例（HEAP表）

```plsql
DECLARE
  clob1 CLOB := 'lob';
  isopen INT;
BEGIN
  isopen := dbms_lob.isopen(clob1);
  dbms_output.put_line(isopen);
  dbms_lob.OPEN(clob1, dbms_lob.lob_readwrite);
  isopen := dbms_lob.isopen(clob1);
  dbms_output.put_line(isopen);
  dbms_lob.CLOSE(clob1);
  isopen := dbms_lob.isopen(clob1);
  dbms_output.put_line(isopen);
END;
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
```

READ过程用于读取LOB指定部分的数据。

| 参数 | 描述                                                                                                                                         |
| :---- |:-------------------------------------------------------------------------------------------------------------------------------------------|
| lob_loc | 待读取的LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW类型。不可为NULL。                                                 |
| amount | 输入为要读取的BLOB字节数或CLOB/NCLOB字符数，至少为1，不能超过65534；输出为实际读取到的BLOB字节数或CLOB/NCLOB字符数。                                                                |
| offset | 读取起点在LOB中的偏移量，BLOB以字节为单位，CLOB/NCLOB以字符为单位，从1开始，不能超过LOB末尾。不可为NULL。                                                                          |
| buffer | 输出读取到的指定部分LOB数据。当读取BLOB时，参数的数据类型可以为RAW/BLOB/CHAR/VARCHAR/NCHAR/NVARCHAR类型；当读取CLOB时，参数的数据类型可以为CLOB/NCLOB/CHAR/VARCHAR/NCHAR/NVARCHAR/RAW类型。 |

示例（单机、共享集群部署）

```plsql
DECLARE
  clob1 CLOB := 'read lob';
  amount INT := 10;
  buffer VARCHAR(10);
BEGIN
  dbms_lob.read(clob1, amount, 6, buffer);
  dbms_output.put_line(buffer);
  dbms_output.put_line(amount);
END;
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

| 参数 | 描述                                                      |
| :---- |:--------------------------------------------------------|
| lob_loc | 待写入的LOB变量，不可为无效的临时LOB。在写入LOB列时需要拥有UPDATE权限。不可为NULL。     |
| amount | 写入BLOB的字节数或写入CLOB/NCLOB的字符数，至少为1，不能超过65534。不可为NULL。     |
| offset | 写入起点在LOB中的偏移量，BLOB以字节为单位，CLOB/NCLOB以字符为单位，从1开始。不可为NULL。 |
| buffer | 待写入LOB的数据，若为LOB类型，不可为无效的临时LOB。不可为NULL。                  |

示例（HEAP表）

```plsql
DECLARE
  clob1 CLOB := 'lob';
  buffer VARCHAR(10) := 'write lob';
BEGIN
  dbms_lob.write(clob1, 5, 5, buffer);
  dbms_output.put_line(clob1);
END;
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


| 参数 | 描述               |
| :---- |:----------------|
| dest_lob | 目标LOB变量，不可为无效的临时LOB。在更新LOB列时需要拥有UPDATE权限。参数的数据类型可以为BLOB/RAW/CLOB/NCLOB/CHAR/VARCHAR/NCHAR/NVARCHAR类型。 |
| src_lob | 源LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB/RAW/CLOB/NCLOB/CHAR/VARCHAR/NCHAR/NVARCHAR类型。                       |

示例（HEAP表）

```plsql
DECLARE
  dest_clob CLOB := 'lob';
  src_clob CLOB := ' append';
BEGIN
  dbms_lob.append(dest_clob, src_clob);
  dbms_output.put_line(dest_clob);
END;
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

| 参数 | 描述                                                                                                     |
| :---- |:-------------------------------------------------------------------------------------------------------|
| lob_loc | 待附加数据的LOB变量，不可为无效的临时LOB。在更新LOB列时需要拥有UPDATE权限。参数的数据类型可以为BLOB/CLOB/VARCHAR/RAW/NCLOB/NVARCHAR类型。不可为NULL。 |
| amount | 附加的BLOB字节数或CLOB/NCLOB字符数，至少为1，不能超过65534。不可为NULL。                                                       |
| buffer | 待附加到LOB末尾的数据，不可为无效的临时LOB。参数的数据类型可以为RAW/BLOB/CHAR/VARCHAR/NCHAR/NVARCHAR类型。                             |

示例（HEAP表）

```plsql
DECLARE
  clob1 CLOB := 'lob';
  buffer VARCHAR(20) := ' append lob';
BEGIN
  dbms_lob.writeappend(clob1, 7, buffer);
  dbms_output.put_line(clob1);
END;
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

| 参数 | 描述              |
| :---- |:-----------------|
| dest_lob | 目标LOB变量，不可为无效的临时LOB。在更新LOB列时需要拥有UPDATE权限。参数的数据类型可以为BLOB/RAW/CLOB/NCLOB/CHAR/VARCHAR/NCHAR/NVARCHAR类型。 |
| src_lob | 源LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB/RAW/CLOB/NCLOB/CHAR/VARCHAR/NCHAR/NVARCHAR类型。         |
| amount | 拷贝的BLOB字节数或CLOB/NCLOB字符数，至少为1。不可为NULL。                                                                |
| dest_offset | 目标LOB偏移量，BLOB以字节为单位，CLOB/NCLOB以字符为单位，从1开始。不可为NULL。                                                    |
| src_offset | 源LOB偏移量，BLOB以字节为单位，CLOB/NCLOB以字符为单位，从1开始。不可为NULL。                                                     |

示例（HEAP表）

```plsql
DECLARE
  clob1 CLOB := 'lob';
BEGIN
  dbms_lob.copy(clob1, 'copy lob', 4, 5, 1);
  dbms_output.put_line(clob1);
END;
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


| 参数 | 描述 |
| :---- | :---- |
| lob_loc | LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW类型。在擦除LOB列时需要拥有UPDATE权限。不可为NULL。 |
| amount | 擦除的BLOB字节数或CLOB/NCLOB字符数，至少为1。不可为NULL。 |
| offset | 待擦除的数据在LOB中的偏移量，BLOB以字节为单位，CLOB/NCLOB以字符为单位，从1开始。不可为NULL。 |

示例（HEAP表）

```plsql
DECLARE
  clob1 CLOB := 'loberaseerase';
  amount INT := 5;
BEGIN
  dbms_lob.erase(clob1, amount, 4);
  dbms_output.put_line(clob1);
END;
/
--result
lob     erase
```

## TRIM

```plsql
DBMS_LOB.TRIM (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB,
   newlen   IN      BIGINT);
```

TRIM过程用于将LOB修剪至指定长度。

| 参数 | 描述                                                                 |
| :---- |:-------------------------------------------------------------------|
| lob_loc | LOB变量，不可为无效的临时LOB。参数的数据类型可以为BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW类型。在修剪LOB列时需要拥有UPDATE权限。不可为NULL。 |
| newlen | 修剪后LOB的新长度，BLOB以字节为单位，CLOB/NCLOB以字符为单位，至少为0，不能超过LOB的长度。不可为NULL。                       |

示例（HEAP表）

```plsql
DECLARE
  clob1 CLOB := 'lob trim newlen';
BEGIN
  dbms_lob.trim(clob1, 8);
  dbms_output.put_line(clob1);
END;
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
```

INSTR函数用于返回pattern在LOB中第n次出现的位置，从LOB的指定位置开始匹配。如果第n次匹配失败，则返回0。

| 参数 | 描述                                                 |
| :---- |:--------------------------------------------------|
| lob_loc | 待匹配的LOB变量。参数的数据类型可以为BLOB/CLOB/CHAR/VARCHAR/RAW/NCLOB/NCHAR/NVARCHAR类型。                                                                  |
| pattern | 待匹配的模式。当匹配BLOB时，参数的数据类型可以为RAW/BLOB/CHAR/VARCHAR/NCHAR/NVARCHAR类型。当匹配CLOB/NCLOB时，参数的数据类型可以为RAW/CLOB/NCLOB/CHAR/VARCHAR/NCHAR/NVARCHAR类型。 |
| offset | 匹配起点在LOB中的偏移量，BLOB以字节为单位，CLOB/NCLOB以字符为单位，从1开始。                                                                                         |
| nth | 匹配次数。                                                                                                                                   |

示例（单机、共享集群部署）

```plsql
DECLARE
  clob1 CLOB := 'lob instr lob instr';
  pos BIGINT;
BEGIN
  pos := dbms_lob.instr(clob1, 'instr', 4, 2);
  dbms_output.put_line(pos);
END;
/
--result
15
```

## 异常

|异常|描述|
| :---- | :---- |
|INVALID_ARGVAL|参数超出数值范围（除数字溢出外）。|
|UNOPENED_FILE|关闭未打开的LOB。|

示例（单机、共享集群部署）

```plsql
--invalid_argval exception
DECLARE
  c1 CLOB:='12345';
BEGIN
  dbms_lob.write(c1,0,1,'6');
EXCEPTION
  WHEN DBMS_LOB.INVALID_ARGVAL THEN
    dbms_output.put_line('argument value out of range');
END;
/
--result
argument value out of range

--unopened_file exception
DECLARE
  c1 CLOB:='12345';
BEGIN
  dbms_lob.CLOSE(c1);
EXCEPTION
  WHEN DBMS_LOB.UNOPENED_FILE THEN
    dbms_output.put_line('try to close unopened lob');
END;
/
--result
try to close unopened lob
```
