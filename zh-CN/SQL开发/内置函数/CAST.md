```ebnf+diagram
cast::= CAST "("expr AS type_name [constraint] [DEFAULT replace_expr ON CONVERSION ERROR] ")"
```

constraint语法如下：
```ebnf+diagram
syntax::= { "(" precision ["," scale] ")" | RANGE low_value ".." high_value}
```

CAST函数将[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)的值转换为指定的数据类型，并按新的类型返回结果，此函数支持基本数据类型和集合类型。

YashanDB支持如下情况的类型转换：

|  expr类型| type_name|
|-----------------------------|----------------------------------------------------------------------|
| BOOLEAN                     | TINYINT, SMALLINT, INT, BIGINT, CHAR, VARCHAR, NCHAR, NVARCHAR              |
| BIT                         | 除FLOAT、DOUBLE外的数值型、字符型数据                                             |
| CHAR、VARCHAR                | 除UDT外的所有数据类型                                                         |
| NCHAR、NVARCHAR              | 除UDT外的所有数据类型                                                  |
| FLOAT、DOUBLE                | 除BIT外的数值型、字符型数据                                                      |
| NUMBER                      | 所有数值型、字符型                                                            |
| TINYINT、SMALLINT、INT、BIGINT | 所有数值型、字符型                                                            |
| TIME                        | 字符型数据、DATE、TIMESTAMP、TIMESTAMP TZ、TIMESTAMP LTZ、INTERVAL DAY TO SECOND |
| DATE                        | 字符型数据、TIMESTAMP、TIMESTAMP TZ、TIMESTAMP LTZ、TIME                      |
| TIMESTAMP                   | 字符型数据、DATE、TIMESTAMP TZ、TIMESTAMP LTZ、TIME                           |
| TIMESTAMP WITH LOCAL TIME   | 字符型数据、DATE、TIMESTAMP、TIMESTAMP TZ、TIME                               |
| TIMESTAMP WITH TIME ZONE    | 字符型数据、DATE、TIMESTAMP、TIMESTAMP LTZ、TIME                              |
| INTERVAL YEAR TO MONTH      | 字符型数据                                                                |
| INTERVAL DAY TO SECOND      | 字符型数据                                                                |
| CLOB                        | 字符型数据、JSON                                                    |
| NCLOB                       | 字符型数据、JSON                                                           |
| BLOB                        | 字符型数据、JSON、RAW、UROWID                                                |
| BFILE                       | 字符型数据、JSON、CLOB、NCLOB、BLOB                                           |
| ROWID                       | 字符型数据、RAW、UROWID                                                     |
| UROWID                      | 字符型数据、RAW                                                            |
| JSON                        | 字符型数据、CLOB、BLOB、NCLOB、RAW                                            |
| RAW                         | 字符型数据、CLOB、BLOB、NCLOB、UROWID                                         |

其中：

* expr的内容需符合目标类型的格式要求。
* expr的值不能超过目标类型的值域，如10000无法转换为TINYINT类型。
* expr的值为NULL时，函数返回NULL。
* 对于列存表中的LOB类型字段，若某行数据为行外存储，则无法使用本函数进行转换。
* 当目标类型为BOOLEAN时，只有'TRUE'、'T'、'YES'、'Y'、'1'可转换为TRUE，'FALSE'、'F'、'NO、'N'、'0'可转换为FALSE，字符不分大小写。
* 当NUMBER转换BIT时，函数将对NUMBER进行向下取整，且NUMBER数据的大小不能超过64位。
* 当TIME转其余时间类型时，取当天的日期来补齐年月日。
* 当expr为数值型常量且目标类型为字符型时，无论cast是否嵌套使用，一旦expr经过cast转换后的数据长度超过字符型的定义长度，函数均会返回报错type size is too small。

**type_name**

指定要转换的目标类型。
在PL块中支持转换为SUBTYPE自定义的子类型，若子类型继承非标量类型或在SQL语句中使用时，将返回错误。

**constraint**

指定数据类型的size、scale、precision信息。

若目标类型为字符型数据时，可以指定其size，若expr为字符串类型且长度大于指定的size会截断，为其他类型超过指定的size，则转换失败并返回Out of range错误。

若目标类型为CHAR或NCHAR时，且语句中未指定其size时，不需要关注需要转换的原始数据类型，其预估size长度默认为1。

若目标类型为VARCHAR时，且语句中未指定其size，将根据expr的类型对size进行预估。对于字符串类型，其预估size为原字符串长度；对于其他类型，其预估size为该类型转为字符串的长度。

若目标类型为NVARCHAR时，须在语句中指定其size，否则返回错误。

若目标类型为整数类型时，仅支持在PL块中对非SQL语句使用RANGE指定范围。

**DEFAULT replace_expr ON CONVERSION ERROR**

表示当对expr转换失败时，使用replace_expr值来进行转换。 replace_expr可以为NULL，字符串值和字面量，且必须能够转换为目标类型（转换类型支持，且转换内容符合格式要求）。

此语句可省略，则对expr转换失败时函数返回错误。

当expr的数据类型为BIT、LOB类型、集合类型，或者type_name为BIT、LOB类型时、集合类型，不允许指定此语句。

  

示例

```sql
-- 基础类型转换
SELECT CAST('345' AS FLOAT) cast1,
CAST('345.2345' AS NUMBER(6,2)) cast2,
CAST('345a' AS INT DEFAULT '' ON CONVERSION ERROR) cast3,
CAST(SYSTIMESTAMP AS CHAR(100)) cast4
FROM DUAL;
      CAST1     CAST2    CAST3 CAST4                     
----------- --------- -------- ---------------------------
  3.45E+002    345.23          2022-01-09 17:52:30.634370

SELECT CAST('abcdef' AS CHAR) cast1,
CAST(1.23456 AS VARCHAR) cast2,
CAST(NULL AS CHAR) cast3,
CAST(NULL AS VARCHAR) cast4
FROM DUAL;
CAST1 CAST2     CAST3 CAST4 
----- --------- ----- ----- 
a     1.23456

SELECT CAST(321.1234567 AS VARCHAR(8)) FROM DUAL;
[1:28]YAS-00008 type convert error : type size is too small

SELECT CAST(CAST(321.1234567 AS NUMBER) AS VARCHAR(8)) FROM DUAL;
[1:44]YAS-00008 type convert error : type size is too small

-- 转换为SUBTYPE自定义的子类型，且使用RANGE约束范围
DECLARE
  SUBTYPE MY_INT IS PLS_INTEGER ;
  v1 PLS_INTEGER;
BEGIN
  v1 := CAST('9' AS MY_INT RANGE 1..10);
  DBMS_OUTPUT.PUT_LINE('value: ' || v1);
END;
/
value: 9

-- SQL语句中转换为SUBTYPE自定义的子类型将返回错误
DECLARE
  SUBTYPE MY_INT IS PLS_INTEGER ;
  v1 PLS_INTEGER;
BEGIN
  SELECT CAST('9' AS MY_INT) INTO v1 FROM DUAL;
  DBMS_OUTPUT.PUT_LINE('value: ' || v1);
END;
/
YAS-04253 PL/SQL compiling errors:
[5:22] YAS-04229 invalid datatype

-- SUBTYPE继承非标量类型时将返回错误
DECLARE
  TYPE MY_REC IS RECORD(a INT);
  SUBTYPE MY_INT IS MY_REC;
  v1 PLS_INTEGER;
BEGIN
  v1 := CAST('9' AS MY_INT);
  DBMS_OUTPUT.PUT_LINE('value: ' || v1);
END;
/
YAS-04253 PL/SQL compiling errors:
[6:9] YAS-04229 invalid datatype

-- 集合类型转换

CREATE OR replace TYPE int_table IS TABLE OF INT;
/
CREATE OR replace TYPE int_table1 IS TABLE OF INT;
/
SELECT * FROM TABLE(CAST(int_table(1,2,3,4) AS int_table1));
COLUMN_VALUE 
------------ 
           1
           2
           3
           4

DROP TYPE int_table;
DROP TYPE int_table1;

-- plsql
CREATE OR replace TYPE varray_01 IS VARRAY(10) OF VARCHAR(20);
/
CREATE OR replace TYPE varray_02 IS VARRAY(10) OF VARCHAR(10);
/
DECLARE
  a varray_01:= varray_01(1,2,3);
  b varray_02:= varray_02(1,2,3);
  BEGIN
  SELECT CAST(a AS varray_02)INTO b FROM dual;
  DBMS_OUTPUT.PUT_LINE('b: ' || b(1) || '  ' || 'b: ' || b(3));
END;
 /
b: 1  b: 3
```
