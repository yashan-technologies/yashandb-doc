DBMS_UTILITY包提供了一组内置的实用存储过程/函数。

## ACTIVE\_INSTANCES

```plsql
TYPE INSTANCE_RECORD IS RECORD (inst_number NUMBER, inst_name VARCHAR(64));
TYPE INSTANCE_TABLE IS TABLE OF INSTANCE_RECORD;

DBMS_UTILITY.ACTIVE_INSTANCES (
   instance_table   OUT INSTANCE_TABLE,
   instance_count   OUT NUMBER);
```

ACTIVE_INSTANCES过程用于查看数据库活跃实例的信息，返回活跃实例的个数以及每个活跃实例的ID和名称。该过程不适用于存算一体分布式集群部署。

|  参数| 描述|
| :--- | :---- |
| instance_table | 包含活动实例编号和名称的列表。当没有实例启动时，列表为空 |
| instance_count | 活跃实例个数 |

示例（单机、共享集群部署）

```plsql
SET serveroutput ON
DECLARE
 inst_record dbms_utility.instance_record;
 inst_tab dbms_utility.instance_table;
 inst_cnt NUMBER;
BEGIN
    dbms_utility.active_instances(inst_tab, inst_cnt);
    FOR i IN 1..inst_cnt LOOP
        DBMS_OUTPUT.put_line(inst_tab(i).inst_number || ' <=> ' || inst_tab(i).inst_name);
    END LOOP;
END;
/

---result
1 <=> yasdb
```

## COMMA\_TO\_TABLE

```plsql
TYPE LNAME_ARRAY IS TABLE of VARCHAR(65534) INDEX BY BINARY_INTEGER;
TYPE UNCL_ARRAY IS TABLE OF VARCHAR(65534) INDEX BY BINARY_INTEGER;

DBMS_UTILITY.COMMA_TO_TABLE ( 
   list   IN  VARCHAR2,
   tablen OUT BINARY_INTEGER,
   tab    OUT uncl_array); 

DBMS_UTILITY.COMMA_TO_TABLE ( 
   list   IN  VARCHAR2,
   tablen OUT BINARY_INTEGER,
   tab    OUT lname_array);
```

COMMA_TO_TABLE过程用于将使用逗号分隔的name字符串转换为数组。该过程不适用于存算一体分布式集群部署。

|  参数| 描述|
| :--- | :---- |
| list | 逗号分隔的'name'列表字符串。根据tab类型不同，name字符串支持以下形式： <br/> \* tab为UNCL_ARRAY类型时，name字符串形式为`identifier[.identifier[.identifier]]@dblink` <br/> \* tab为LNAME_ARRAY类型时，name字符串形式为 `identifier[.identifier]*` |
| tablen | 分割的name数 |
| tab | name列表字符串转换成的关联数组 |

示例（单机、共享集群部署）

```plsql
DECLARE
    l_list    VARCHAR(4000) := '  c  ,     a.b.c    , dd@dd@"k"  ';
    l_count   BINARY_INTEGER;
    l_array   DBMS_UTILITY.UNCL_ARRAY;
BEGIN
    -- 将逗号分隔的字符串转换为表
    DBMS_UTILITY.COMMA_TO_TABLE(list => l_list, tablen => l_count, tab => l_array);
    
    -- 输出转换后的表的元素数
    DBMS_OUTPUT.PUT_LINE('Number of elements: ' || TO_CHAR(l_count));
	DBMS_OUTPUT.PUT_LINE('1:' || l_array(1) || 'end' );
	DBMS_OUTPUT.PUT_LINE('2:' || l_array(2) || 'end');
    DBMS_OUTPUT.PUT_LINE('3:' || l_array(3) || 'end' );
    
END;
/

--result
Number of elements: 3
1:  c  end
2:     a.b.c    end
3: dd@dd@"k"  end
```

## CURRENT\_INSTANCE

```plsql
DBMS_UTILITY.CURRENT_INSTANCE()
   RETURN NUMBER;
```

CURRENT_INSTANCE函数用于获取当前连接的实例号。

示例

```plsql
SELECT dbms_utility.current_instance() FROM dual;

CURRENT_INSTANCE
----------------
           1
```

## DB\_VERSION

```plsql
dbms_utility.db_version (
version       OUT VARCHAR2,
compatibility OUT VARCHAR2);
```

DB_VERSION过程用于获取数据库的版本信息。

|  参数| 描述|
| :--- | :---- |
| version | 一个字符串，表示数据库的内部软件版本 |
| compatibility | 保留参数，仅用于兼容，参数值无实际影响 |

示例

```plsql
SET serveroutput ON
DECLARE
 ver    CHAR(16);
 compat CHAR(12);
BEGIN
  dbms_utility.db_version(ver, compat);
  dbms_output.put_line('Version: ' || ver ||' Compatible: ' || compat);
END;
/

--- result
Version: 23.4.1.100        Compatible:
```

## EXEC\_DDL\_STATEMENT

```plsql
DBMS_UTILITY.EXEC_DDL_STATEMENT (
  parse_string IN VARCHAR2
  );
```

EXEC_DDL_STATEMENT过程用于执行DDL语句。该过程不适用于存算一体分布式集群部署。

|  参数| 描述|
| :--- | :---- |
| parse_string | 待执行的语句 |

示例
```plsql
DECLARE
BEGIN
    DBMS_UTILITY.EXEC_DDL_STATEMENT('CREATE TABLE UTILITY_TABLE(A INT, B INT)');
END;
/
SELECT A,B FROM UTILITY_TABLE;

--result
           A            B
------------ ------------

```

## FORMAT\_CALL\_STACK

```plsql
DBMS_UTILITY.FORMAT_CALL_STACK() 
  RETURN VARCHAR;
```

FORMAT_CALL_STACK函数返回当前过程体调用栈的格式化输出，无参数，'()'可以省略，返回值最大2000个字节。

示例

```plsql
CREATE OR REPLACE PROCEDURE PROC_CALL AS
BEGIN
DBMS_OUTPUT.PUT_LINE(DBMS_UTILITY.FORMAT_CALL_STACK);
END;
/

CALL PROC_CALL;

--result
----- PL/SQL Call Stack ----
object handle     line number  object  name
0x7fffe2e39010              3  procedure  REGRESS.PROC_CALL
0x7fffdfb3bd90              1  anonymous block  
```

## FORMAT\_ERROR\_BACKTRACE

```plsql
DBMS_UTILITY.FORMAT_ERROR_BACKTRACE()
  RETURN VARCHAR2;
```

FORMAT_ERROR_BACKTRACE函数显示引发异常的位置，最大长度为4000字节。

示例

```plsql
SET serveroutput ON
CREATE OR REPLACE PROCEDURE format_error_backtrace_p0 IS
   str VARCHAR(4000);
BEGIN
    str := 1/0;
   DBMS_OUTPUT.PUT_LINE('Hello, this is my first stored procedure!');
END format_error_backtrace_p0;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_p1 IS
BEGIN
  format_error_backtrace_p0();
END format_error_backtrace_p1;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_p2 IS
BEGIN
  format_error_backtrace_p1();
END format_error_backtrace_p2;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_p3 IS
BEGIN
  format_error_backtrace_p2();
END format_error_backtrace_p3;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_p4 IS
BEGIN
  format_error_backtrace_p3();
END format_error_backtrace_p4;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_p5 IS
BEGIN

  format_error_backtrace_p4();
END format_error_backtrace_p5;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_top_nolog IS
BEGIN
  format_error_backtrace_p5();
END format_error_backtrace_top_nolog;
/

CREATE OR REPLACE PROCEDURE format_error_backtrace_top_logging IS
BEGIN
  format_error_backtrace_p5();
EXCEPTION
WHEN others THEN
    dbms_output.put_line('Error_Backtrace...' || CHR(10) ||
    dbms_utility.format_error_backtrace());
    dbms_output.put_line( '----------' );
END format_error_backtrace_top_logging;
/

exec format_error_backtrace_top_logging;

--- result 
Error_Backtrace...
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P0", line 4
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P1", line 3
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P2", line 3
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P3", line 3
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P4", line 3
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_P5", line 4
YAS-04015 at "SYS.FORMAT_ERROR_BACKTRACE_TOP_LOGGING", line 3

----------
```

## FORMAT\_ERROR\_STACK

```plsql
DBMS_UTILITY.FORMAT_ERROR_STACK()
  RETURN VARCHAR;
```

FORMAT_ERROR_STACK函数返回当前错误栈的格式化输出，无参数，'()'可以省略，返回值最大2000个字节。

示例

```plsql
DECLARE
N INT;
BEGIN
N := 10 / 0;
EXCEPTION
WHEN OTHERS THEN
DBMS_OUTPUT.PUT_LINE(DBMS_UTILITY.FORMAT_ERROR_STACK);
END;
/

--result
YAS-00011 divided by zero
```

## GET\_ENDIANNESS

```plsql
DBMS_UTILITY.GET_ENDIANNESS()
  RETURN NUMBER;
```

GET_ENDIANNESS函数用于获取当前数据库平台的字节序，返回值为1表示大端字节序，返回值为2表示小端字节序。

示例
```plsql
SELECT DBMS_UTILITY.GET_ENDIANNESS() FROM dual;
```

## GET\_HASH\_VALUE

```plsql
DBMS_UTILITY.GET_HASH_VALUE (
  NAME       IN  VARCHAR, 
  BASE       IN  NUMBER, 
  HASH_SIZE  IN  NUMBER)
  RETURN NUMBER;
```

GET_HASH_VALUE函数为字符串计算一个落在给定范围内的哈希值，该范围是`[BASE, BASE + HASH_SIZE - 1]`。

|  参数| 描述|
| :--- | :---- |
| NAME | 需要计算哈希值的字符串，不允许为NULL或空字符串 |
| BASE | 哈希值的起始值 |
| HASH_SIZE | 哈希值的大小 |

使用说明：

- BASE和HASH_SIZE的取值范围是\[-2<sup>31</sup>, 2<sup>31</sup> - 1\]。
- BASE取值如果是浮点数，会四舍五入取整。
- HASH_SIZE取值不能是0或浮点数。

示例

```plsql
SELECT DBMS_UTILITY.GET_HASH_VALUE('abc', 1000, 2048) FROM dual;
```

## GET\_PARAMETER\_VALUE

```plsql
DBMS_UTILITY.GET_PARAMETER_VALUE (
   parnam     IN        VARCHAR2,
   intval     IN OUT    BINARY_INTEGER,
   strval     IN OUT    VARCHAR2,
   listno     IN        BINARY_INTEGER DEFAULT 1)
  RETURN BINARY_INTEGER;
```

GET_PARAMETER_VALUE函数将获取系统参数的值，对应值可以在V$PARAMETER上查到。

如果参数类型为INTEGER/BOOL时，返回值为0。如果类型是VARCHAR，则返回是1。

|  参数| 描述|
| :--- | :---- |
| param | 参数名称 |
| intval | 如果参数值是INTEGER/BOOL类型，则返回对应值。如果参数值是字符串类型，则返回对应值的长度 |
| strval | 如果参数值是INTEGER/BOOL类型，则返回值是NULL。如果参数值是字符串类型，则返回对应值 |
| listno | 保留参数，仅用于兼容，参数值无实际影响 |

示例

```plsql
DECLARE
  -- 参数名、整数值、字符串值及参数类型
  parnam  VARCHAR2(256) := 'DSINTERVAL_FORMAT';  -- 参数名称
  intval  BINARY_INTEGER;  -- 可存储整数参数的值或字符串参数值的长度
  strval  VARCHAR2(256);  -- 可存储字符串参数的值
  partyp  BINARY_INTEGER;  -- 参数类型标识符
BEGIN
  -- 使用GET_PARAMETER_VALUE函数检索参数值及类型
  partyp := DBMS_UTILITY.GET_PARAMETER_VALUE(parnam, intval, strval);
  
  -- 输出参数值
  DBMS_OUTPUT.PUT('Parameter value is: ');
  IF partyp = 1 THEN
    DBMS_OUTPUT.PUT_LINE(strval);  -- 如果是字符串参数，输出字符串值
  ELSE
    DBMS_OUTPUT.PUT_LINE(TO_CHAR(intval));  -- 如果是整数参数，输出整数值
  END IF;

  -- 如果是字符串参数，还可以输出该字符串的长度
  IF partyp = 1 THEN
    DBMS_OUTPUT.PUT('Parameter value length is: ');
    DBMS_OUTPUT.PUT_LINE(intval);  -- 输出字符串值的长度
  END IF;

  -- 输出参数类型（整数或字符串）
  DBMS_OUTPUT.PUT('Parameter type is: ');
  IF partyp = 1 THEN
    DBMS_OUTPUT.PUT_LINE('string');
  ELSE
    DBMS_OUTPUT.PUT_LINE('integer');
  END IF;
END;
/

--result
Parameter value is: dd hh24:mi:ss.ff
Parameter value length is: 16
Parameter type is: string
```

## GET\_SQL\_HASH

```plsql
DBMS_UTILITY.GET_SQL_HASH (
   name          IN   VARCHAR2,
   hash          OUT  RAW,
   pre10ihash    OUT  NUMBER)
  RETURN NUMBER;
```

GET_SQL_HASH函数使用MD5算法计算给定字符串的哈希值。

|  参数| 描述|
| :--- | :---- |
| name | 要进行哈希计算的字符串 |
| hash | 用于存储所有返回16字节哈希值的可选字段 |
| pre10ihash | 不支持此参数，返回结果为NULL |

示例

```plsql
SET serveroutput ON

DECLARE
 h VARCHAR(32);
 n NUMBER;
 x NUMBER;
BEGIN
  x :=  dbms_utility.get_sql_hash('select 1 from t1', h, n);

  dbms_output.put_line('Return Value: ' || x);
  dbms_output.put_line('Hash: ' || h);
  dbms_output.put_line('Pre10iHash: ' || TO_CHAR(n));
END;
/

--- result
Return Value: 1334230816
Hash: 1E7E2F21208F80F4BEB552F320BF864F
Pre10iHash: 
```

## GET\_TIME

```plsql
DBMS_UTILITY.GET_TIME()
  RETURN NUMBER;
```

GET_TIME函数返回一个数值（本地时间戳）用于表示当前时间，以百分之一秒为单位。

该数值与系统当前时间相关，可能为负数。

示例
```plsql
-- 获取当前时间
SELECT DBMS_UTILITY.GET_TIME() FROM dual;

-- 输出 200hsec = 2 sec
SET serveroutput ON;
DECLARE
  start_time NUMBER;
  end_time NUMBER;
BEGIN
  start_time:= DBMS_UTILITY.GET_TIME();
  DBMS_LOCK.SLEEP(2);
  end_time:= DBMS_UTILITY.GET_TIME() - start_time;
  DBMS_OUTPUT.PUT_LINE('elapsed = ' || end_time || 'hsecs');
END;
/
SET serveroutput OFF;
```

## IS\_BIT\_SET

```plsql
DBMS_UTILITY.IS_BIT_SET (
  R     IN    RAW,
  N     IN    NUMBER)
  RETURN NUMBER;
```

IS_BIT_SET函数检查给定的RAW变量对应位置的比特(bit)是否被设置。

|  参数| 描述|
| :--- | :---- |
| R | 需要检查的RAW变量 |
| N | 需要检查的位置，N的取值只能为正整数且小于RAW变量的比特位长度 |

示例

```plsql
SELECT DBMS_UTILITY.IS_BIT_SET(HEXTORAW('AB'), 1) FROM DUAL;
```

## IS\_CLUSTER\_DATABASE

```plsql
DBMS_UTILITY.IS_CLUSTER_DATABASE()
  RETURN BOOLEAN;
```

IS_CLUSTER_DATABASE函数判断数据库是否处于共享集群模式，如果是就返回TRUE，否则返回FALSE。

示例

```plsql
SET serveroutput ON;
BEGIN
  IF DBMS_UTILITY.IS_CLUSTER_DATABASE THEN
    DBMS_OUTPUT.PUT_LINE('TRUE');
  ELSE
    DBMS_OUTPUT.PUT_LINE('FALSE');
  END IF;
END;
/
SET serveroutput OFF;
```

## NAME\_RESOLVE

```plsql
DBMS_UTILITY.NAME_RESOLVE (
   name          IN  VARCHAR, 
   context       IN  NUMBER,
   schema        OUT VARCHAR, 
   part1         OUT VARCHAR, 
   part2         OUT VARCHAR,
   dblink        OUT VARCHAR, 
   part1_type    OUT NUMBER, 
   object_number OUT NUMBER);
```

本过程的输入形式等同于NAME_TOKENIZE过程。本过程尝试将name解析成a，b，c，dblink四个部分，并根据context指定的值，确认a，b，c，dblink的实际含义。

|  参数| 描述|
| :--- |:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name | 对象名，格式为[[a.]b.]c[@d]，其中a、b、c均为SQL对象，d为dblink                                                                                                                                    |
| context | [0,9]范围的整数，指定解析的模式： <br> \* 0 - table：表<br/> \* 1 - function/procedure/package：函数/过程/包<br/> \* 2 - sequence：序列<br/> \* 3 - trigger：触发器<br/> \* 7 - type：类型<br/> \* 9 - index：索引 |
| schema | 对象的schema，如果未指定，名字由对象所属schema决定                                                                                                                                                 |
| part1 | 名字的第一部分，此名称的类型指定为part1_type（同义词或包）                                                                                                                                              |
| part2 | 如果该值为非NULL，则表示子程序名。如果part1为非NULL，则part2指示的子程序位于part1所指示的包内；如果part1为NULL，则子程序是顶级子程序                                                                                              |
| dblink | 如果该值为非NULL，则表示数据库链接被指定为name的一部分，或者name是一个同义词，该同义词解析为具有数据库链接的内容。在这种情况下，如需进一步进行名称转换（即执行远程节点上的NAME_RESOLVE过程），应调用DBMS_UTILITY此远程节点上的NAME_RESOLVE过程                                 |
| part1_type | 第一部分的[类型](../../../参考手册/系统视图/DBA视图/DBA_OBJECTS)                                                                                                                                 |
| object_number | 对象ID                                                                                                                                                                            |

过程使用规则：

- 当对象名[[a.]b.]c[@d]中的dblink不为空时，NAME_RESOLVE退化为NAME_TOKENIZE，仅解析并输出schema，part1，part2和dblink四个部分。
- 当对象名[[a.]b.]c[@d]中的dblink为空时，会从a，b和c三个部分中解析出schema，objectName，c部分仅当context为1时，对package对象有效。
- 当对象名[[a.]b.]c[@d]中仅有a不为空时，则a为objectName，schema为当前用户名；当对象名[[a.]b.]c[@d]中a，b都不为空时，则a为schema，b为objectName。随后根据c部分的context值获取对应类型的对象oid，并通过object_number参数返回对应的oid、part1_type参数返回对应的对象类型，若无法获取到对应类型的对象则报错。

示例

```plsql
-- 以sales用户的area表为例
DECLARE
   l_schema VARCHAR2(30);
   l_part1 VARCHAR2(30);
   l_part2 VARCHAR2(30);
   l_dblink VARCHAR2(30);
   l_part1_type NUMBER;
   l_object_number NUMBER;
BEGIN
   DBMS_UTILITY.NAME_RESOLVE('area', 0, l_schema, l_part1, l_part2, l_dblink, l_part1_type, l_object_number);
   DBMS_OUTPUT.PUT_LINE('Schema: ' || l_schema);
   DBMS_OUTPUT.PUT_LINE('Part1: ' || l_part1);
   DBMS_OUTPUT.PUT_LINE('Part2: ' || l_part2);
   DBMS_OUTPUT.PUT_LINE('DB Link: ' || l_dblink);
   DBMS_OUTPUT.PUT_LINE('Part1 Type: ' || l_part1_type);
   DBMS_OUTPUT.PUT_LINE('Object Number: ' || l_object_number);
END;
/

--result
Schema: SALES
Part1: AREA
Part2:
DB Link:
Part1 Type: 1
Object Number: 2426
```

## NAME\_TOKENIZE

```plsql
DBMS_UTILITY.NAME_TOKENIZE ( 
   name    IN  VARCHAR,
   a       OUT VARCHAR,
   b       OUT VARCHAR,
   c       OUT VARCHAR,
   dblink  OUT VARCHAR, 
   nextpos OUT BINARY_INTEGER);
```

NAME_TOKENIZE过程使用parser将输入`identifier[.identifier[.identifier]][@dblink]`形式的name输出为a、b、c和dblink，其中dblink的形式为`identifier[.identifier]*[@identifier]`。对于非双引号的**identifier**将转换成大写。

|  参数| 描述|
| :--- | :---- |
| name | 由sql identifier组成，形如`scott.foo@dblink` |
| a | 第一部分token |
| b | 第二部分token |
| c | 第三部分token |
| dblink | @之后的dblink部分 |
| nextpos | parse终止的字符串下标，例如`a.b c`，则nextpos为3 |

示例
```plsql
DECLARE
   name VARCHAR(20000) := 'a.d.c@rdb';
   a VARCHAR(2000);
   b VARCHAR(2000);
   c VARCHAR(2000);
   dblink VARCHAR(2000);
   nextpos BINARY_INTEGER;
BEGIN
   DBMS_UTILITY.NAME_TOKENIZE(
      name => name,
      a => a,
      b => b,
      c => c,
      dblink => dblink,
      nextpos => nextpos
   );
   DBMS_OUTPUT.PUT_LINE('A: ' || a);
   DBMS_OUTPUT.PUT_LINE('B: ' || b);
   DBMS_OUTPUT.PUT_LINE('C: ' || c);
   DBMS_OUTPUT.PUT_LINE('DBLink: ' || dblink);
   DBMS_OUTPUT.PUT_LINE('Nextpos: ' || nextpos);
END;
/

--result
A: A
B: D
C: C
DBLink: RDB
Nextpos: 9
```

## OLD\_CURRENT\_SCHEMA

```plsql
DBMS_UTILITY.OLD_CURRENT_SCHEMA()
 RETURN VARCHAR;
```

OLD_CURRENT_SCHEMA函数返回当前会话的SCHEMA。

示例

```plsql
SELECT DBMS_UTILITY.OLD_CURRENT_SCHEMA() FROM dual;
```

## OLD\_CURRENT\_USER

```plsql
DBMS_UTILITY.OLD_CURRENT_USER()
 RETURN VARCHAR;
```

OLD_CURRENT_USER函数返回当前会话的USER。

示例

```plsql
SELECT DBMS_UTILITY.OLD_CURRENT_USER() FROM dual;
```

## PORT\_STRING

```plsql
DBMS_UTILITY.PORT_STRING()
  RETURN VARCHAR;
```

PORT_STRING函数返回操作系统的平台以及版本。

示例

```plsql
SELECT DBMS_UTILITY.PORT_STRING() FROM dual;
```

## TABLE\_TO\_COMMA

```plsql
DBMS_UTILITY.TABLE_TO_COMMA ( 
   tab    IN  UNCL_ARRAY, 
   tablen OUT BINARY_INTEGER,
   list   OUT VARCHAR2);

DBMS_UTILITY.TABLE_TO_COMMA ( 
   tab    IN  LNAME_ARRAY,
   tablen OUT BINARY_INTEGER,
   list   OUT VARCHAR2);
```

TABLE_TO_COMMA过程将UNCL_ARRAY或者是LNAME_ARRAY类型的name数组转换成逗号分隔的字符串。该过程不适用于存算一体分布式集群部署。

该过程不会对tab的输入进行校验。

|  参数| 描述|
| :--- | :---- |
| tab | 'name'的关联数组 |
| tablen | 数组长度 |
| list | 返回的逗号分隔的'name'的字符串 |

示例（单机、共享集群部署）

```plsql
DECLARE
   -- 使用DBMS_UTILITY.LNAME_ARRAY类型
   myArray DBMS_UTILITY.UNCL_ARRAY;
   
   -- 用于存储转换后的字符串和数组长度
   listOut VARCHAR(4000);
   tabLen  BINARY_INTEGER;
BEGIN
   -- 向数组中添加数据
   myArray(1) := '++++ ++++          +++         +++ ';
   myArray(2) := '1111111100000000000001111111111111111';

   -- 调用TABLE_TO_COMMA过程
   DBMS_UTILITY.TABLE_TO_COMMA(
      tab    => myArray, 
      tablen => tabLen,
      list   => listOut
   );

   -- 输出结果
   DBMS_OUTPUT.PUT_LINE('Comma-separated list: ' || listOut);
   DBMS_OUTPUT.PUT_LINE('Number of elements: ' || tabLen);
END;
/

--result
iComma-separated list: ++++ ++++          +++         +++ ,1111111100000000000001111111111111111
Number of elements: 2

```
