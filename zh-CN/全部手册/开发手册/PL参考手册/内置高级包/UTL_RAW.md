UTL_RAW包提供了操作RAW数据类型的SQL函数。

## BIT\_AND

```plsql
UTL_RAW.BIT_AND (
   r1 IN RAW,
   r2 IN RAW) 
RETURN RAW;
```

BIT_AND函数用于对RAW r1和RAW r2中的值执行逐位逻辑“与”并返回执行结果RAW。

|  参数| 描述|
| :--- | :-------------------------- |
| r1 | RAW类型的值，允许为''或NULL |
| r2 | RAW类型的值，允许为''或NULL |

函数使用规则：

- 若某个输入参数的值为''或NULL，返回结果为NULL。
- 如果r1和r2长度不同，逐位逻辑“与”操作截止于较短RAW的最后一个字节，较长RAW的多余部分将直接附加在后面，最终返回结果RAW的长度与较长RAW一致。

示例

```plsql
SELECT utl_raw.bit_and('ff01','f02') FROM dual;

UTL_RAW.BIT_AND('FF0                                             
---------------------------------------------------------------- 
0F00                                                            


SELECT utl_raw.bit_and('ff01','02') FROM dual;

UTL_RAW.BIT_AND('FF0                                             
---------------------------------------------------------------- 
0201                                                            

```

## BIT\_COMPLEMENT

```plsql
UTL_RAW.BIT_COMPLEMENT (
   r IN RAW) 
  RETURN RAW;
```

BIT_COMPLEMENT函数用于对RAW r中的值执行逐位逻辑“补码”，并返回补码结果RAW。结果长度等于输入的RAW r长度。

|  参数| 描述|
| :--- | :-------------------------- |
| r  | RAW类型的值，允许为''或NULL |

函数使用规则：

- 如果输入为null或''，则返回null。

```plsql
SELECT utl_raw.BIT_COMPLEMENT('') FROM dual;

UTL_RAW.BIT_COMPLEME                                             
---------------------------------------------------------------- 


SELECT utl_raw.BIT_COMPLEMENT('ff00') FROM dual;

UTL_RAW.BIT_COMPLEME                                             
---------------------------------------------------------------- 
00FF                                                            

```

## BIT\_OR

```plsql
UTL_RAW.BIT_OR (
   r1 IN RAW,
   r2 IN RAW) 
  RETURN RAW;
```

BIT_OR函数用于对RAW r1和RAW r2中的值执行逐位逻辑“或”，并返回执行结果RAW。

|  参数| 描述|
| :--- | :-------------------------- |
| r1 | RAW类型的值，允许为''或NULL |
| r2 | RAW类型的值，允许为''或NULL |

函数使用规则：

- 如果r1和r2长度不同，”或“操作截止较短的raw的最后一个字节，较长的raw的将直接附加在后面，结果长度等于较长的raw。
- 返回r1和r2的“或”操作结果，如果r1或者r2的输入为null或''， 返回null。

示例

```plsql
SELECT utl_raw.bit_or('ff01', 'f02') FROM dual;

UTL_RAW.BIT_OR('FF01                                             
---------------------------------------------------------------- 
FF03                                                            

```

## BIT\_XOR

```plsql
UTL_RAW.BIT_XOR (
   r1 IN RAW,
   r2 IN RAW) 
  RETURN RAW;
```

BIT_XOR函数用于对RAW r1和RAW r2中的值执行逐位逻辑“异或”，并返回执行结果RAW。

|  参数| 描述|
| :--- | :-------------------------- |
| r1 | RAW类型的值，允许为''或NULL |
| r2 | RAW类型的值，允许为''或NULL |

函数使用规则：

- 如果r1和r2长度不同，”异或“操作截止较短的raw的最后一个字节，较长的raw的将直接附加在后面，结果长度等于较长的raw。
- 返回r1和r2的”异或“操作结果，如果r1或者r2的输入为null或''，返回null。

示例

```plsql
SELECT utl_raw.bit_xor('ff01', 'f02') FROM dual;

UTL_RAW.BIT_XOR('FF0                                             
---------------------------------------------------------------- 
F003                                                            

```

## CAST\_FROM\_BINARY\_DOUBLE

```plsql
UTL_RAW.CAST_FROM_BINARY_DOUBLE(
   n          IN BINARY_DOUBLE,
   endianess  IN PLS_INTEGER DEFAULT 1) 
RETURN RAW;
```

CAST_FROM_BINARY_DOUBLE函数用于将DOUBLE类型转换成RAW，内存格式遵从IEEE754标准。

|  参数| 描述|
| ----------- | ------------------------------------------------------------ |
| n         | BINARY_DOUBLE类型的值                                        |
| endianess | 表示端序的BINARY_INTEGER值，取值如下：<br/>\* 1或big_endian：表示采用大端序，默认为该值<br/>\* 2或little_endian：表示采用小端序<br/>\* 3或machine_endian：表示采用YashanDB服务端的端序 |

示例

```plsql
SELECT utl_raw.cast_from_binary_double(123.45, 1) AS a FROM dual;

A                 
----------------- 
405EDCCCCCCCCCCD 

```

## CAST\_FROM\_BINARY\_FLOAT

```plsql
UTL_RAW.CAST_FROM_BINARY_FLOAT(
   n          IN BINARY_FLOAT,
   endianess  IN PLS_INTEGER DEFAULT 1) 
RETURN RAW;
```

CAST_FROM_BINARY_FLOAT函数用于将FLOAT类型转换成RAW。

|  参数| 描述|
| ----------- | ------------------------------------------------------------ |
| n         | BINARY_FLOAT类型的值                                         |
| endianess | 表示端序的BINARY_INTEGER值，取值如下：<br/>\* 1或big_endian：表示采用大端序，默认为该值<br/>\* 2或little_endian：表示采用小端序<br/>\* 3或machine_endian：表示采用YashanDB服务端的端序 |

示例

```plsql
SELECT utl_raw.cast_from_binary_float(123.45,1) AS a FROM   dual;

A         
--------- 
42F6E666 

```

## CAST\_FROM\_BINARY\_INTEGER

```plsql
UTL_RAW.CAST_FROM_BINARY_INTEGER (
   n          IN BINARY_INTEGER
   endianess  IN PLS_INTEGER DEFAULT BIG_ENDIAN) 
RETURN RAW;
```

CAST_FROM_BINARY_INTEGER函数用于将INTEGER转换成RAW类型。

|  参数| 描述|
| ----------- | ------------------------------------------------------------ |
| n         | BINARY_INTEGER类型的值                                       |
| endianess | 表示端序的BINARY_INTEGER值，取值如下：<br/>\* 1或big_endian：表示采用大端序，默认为该值<br/>\* 2或little_endian：表示采用小端序<br/>\* 3或machine_endian：表示采用YashanDB服务端的端序 |

示例

```plsql
SELECT utl_raw.cast_from_binary_integer(13423,1)  AS a FROM dual;

A         
--------- 
0000346F 

```

## CAST\_FROM\_NUMBER

```PLSQL
UTL_RAW.CAST_FROM_NUMBER (
n IN NUMBER)
RETURN RAW;
```

CAST_FROM_NUMBER函数用于将NUMBER类型转换成RAW格式。

|  参数| 描述|
| ---- | -------------- |
| n    | NUMBER类型的值 |

示例

```plsql
SELECT utl_raw.cast_from_number(123456.789) AS a FROM dual;

A             
------------- 
C30D23394F5B 

```

## CAST\_TO\_NUMBER

``` plsql
UTL_RAW.CAST_TO_NUMBER (
   r  IN RAW) 
 RETURN NUMBER;
```

CAST_TO_NUMBER函数用于将RAW类型转换成NUMBER。

|  参数| 描述|
| ---- | --------------------------- |
| r    | RAW类型的值，允许为''或NULL |

示例

```plsql
SELECT utl_raw.cast_to_number(utl_raw.cast_from_number(123456.789)) AS a FROM dual;

          A 
----------- 
 123456.789

```

## CAST\_TO\_NVARCHAR2

```plsql
UTL_RAW.CAST_TO_NVARCHAR2 (
   r IN RAW) 
RETURN NVARCHAR2;
```

CAST_TO_NVARCHAR2函数用于使用数据库默认的字符集将RAW类型转换成NVARCHAR2格式。

|  参数| 描述|
| ---- | --------------------------- |
| r  | RAW类型的值，允许为''或NULL |

示例

```plsql
SELECT utl_raw.cast_to_nvarchar2('20142014')  AS a FROM dual;

A
----------------------------------------------------------------
——

```

## CAST\_TO\_RAW

```plsql
UTL_RAW.CAST_TO_RAW (
   c  IN VARCHAR2) 
RETURN RAW;
```

CAST_TO_RAW函数用于将VARCHAR2类型的数据转化为二进制数据格式，此操作仅改变数据的格式不改变数据的值。

|  参数| 描述|
| ---- | ---------------- |
| `c`  | VARCHAR2类型的值 |

示例

```plsql
SELECT utl_raw.cast_to_RAW('aA1234567890')  AS a FROM dual;

A                         
------------------------- 
614131323334353637383930 

```

## CAST\_TO\_VARCHAR2

```plsql
UTL_RAW.CAST_TO_VARCHAR2 (
   r IN RAW) 
RETURN VARCHAR2;
```

CAST_TO_VARCHAR2函数用于使用数据库默认的字符集将RAW类型转换成VARCHAR2格式。

|  参数| 描述|
| ---- | --------------------------- |
| r  | RAW类型的值，允许为''或NULL |

示例

```plsql
SELECT utl_raw.cast_to_varchar2(utl_raw.cast_to_RAW('——'))  AS a FROM dual;

A     
----- 
——   

```

## CAST\_TO\_BINARY\_DOUBLE

```plsql
UTL_RAW.CAST_TO_BINARY_DOUBLE (
   r          IN RAW
   endianess  IN PLS_INTEGER DEFAULT 1) 
RETURN BINARY_DOUBLE;
```

CAST_TO_BINARY_DOUBLE函数用于将RAW类型转换为DOUBLE类型，满足IEEE754标准。

|  参数| 描述|
| ----------- | ------------------------------------------------------------ |
| r         | BINARY_DOUBLE类型的值                                        |
| endianess | 表示端序的BINARY_INTEGER值，取值如下：<br/>\* 1或big_endian：表示采用大端序，默认为该值<br/>\* 2或little_endian：表示采用小端序<br/>\* 3或machine_endian：表示采用YashanDB服务端的端序 |

示例

```plsql
SELECT utl_raw.cast_to_binary_double(utl_raw.cast_from_binary_double(123.45, 2), 2) AS a FROM dual;

          A 
----------- 
 1.235E+002

```

## CAST\_TO\_BINARY\_FLOAT

```plsql
UTL_RAW.CAST_TO_BINARY_FLOAT (
   r          IN RAW
   endianess  IN PLS_INTEGER DEFAULT 1) 
RETURN BINARY_FLOAT;
```

CAST_TO_BINARY_FLOAT函数用于将RAW类型转换为FLOAT类型，满足IEEE754标准。

|  参数| 描述|
| ----------- | ------------------------------------------------------------ |
| r         | BINARY_FLOAT类型的值                                        |
| endianess | 表示端序的BINARY_INTEGER值，取值如下：<br/>\* 1或big_endian：表示采用大端序，默认为该值<br/>\* 2或little_endian：表示采用小端序<br/>\* 3或machine_endian：表示采用YashanDB服务端的端序 |

示例

```plsql
SELECT utl_raw.cast_to_binary_float(utl_raw.cast_from_binary_float(123.45, 2), 2) AS a FROM dual;

          A 
----------- 
 1.235E+002

```

## CAST\_TO\_BINARY\_INTEGER

```plsql
UTL_RAW.CAST_TO_BINARY_INTEGER (
   r          IN RAW
   endianess  IN PLS_INTEGER DEFAULT BIG_ENDIAN) 
RETURN BINARY_INTEGER;
```

CAST_TO_BINARY_INTEGER函数用于将RAW类型转换为INTEGER类型。

|  参数| 描述|
| ----------- | ------------------------------------------------------------ |
| r         | BINARY_INTEGER的值                                           |
| endianess | 表示端序的BINARY_INTEGER值，取值如下：<br/>\* 1或big_endian：表示采用大端序，默认为该值<br/>\* 2或little_endian：表示采用小端序<br/>\* 3或machine_endian：表示采用YashanDB服务端的端序 |

示例

```plsql
SELECT utl_raw.CAST_TO_BINARY_INTEGER('0000346F', 1) AS a FROM dual;

           A 
------------ 
       13423

```

## COMPARE

```plsql
UTL_RAW.COMPARE (
   r1  IN RAW,
   r2  IN RAW,
   pad IN RAW DEFAULT NULL) 
  RETURN NUMBER;
```

COMPARE函数用于比较两个RAW值。如果它们的长度不同，则根据可选的填充参数在右侧延伸较短的RAW。

|  参数| 描述|
| :---- | :----------------------------------------------------------- |
| r1  | RAW类型的值，允许为''或NULL                                  |
| r2  | RAW类型的值，允许为''或NULL                                  |
| `pad` | RAW DEFAULT NULL 用于比较时较短者的填充补齐字段，默认为NULL，表示'00' |

函数使用规则：

- 比较r1 和 r2，相等返回0，否则返回第一个不匹配的位置。
- 如果长度不等则对较短的raw进行扩展，默认使用'00'。
- pad可以设置超过一个字节但是仅选取第一个字节。
- r1，r2，pad均可以输入''或null，pad输入''时仍然使用'00'。

示例

```PLSQL
SELECT utl_raw.compare('ff0102', 'ff', '0102') FROM dual;

UTL_RAW.COMPARE('FF0 
-------------------- 
                   3


SELECT utl_raw.compare('ff0002', 'ff') FROM dual;

UTL_RAW.COMPARE('FF0 
-------------------- 
                   3

```

## CONCAT

```plsql
UTL_RAW.CONCAT (  
   r1  IN RAW DEFAULT NULL,
   r2  IN RAW DEFAULT NULL,
   r3  IN RAW DEFAULT NULL,
   r4  IN RAW DEFAULT NULL,
   r5  IN RAW DEFAULT NULL,
   r6  IN RAW DEFAULT NULL,
   r7  IN RAW DEFAULT NULL,
   r8  IN RAW DEFAULT NULL,
   r9  IN RAW DEFAULT NULL,
   r10 IN RAW DEFAULT NULL,
   r11 IN RAW DEFAULT NULL,
   r12 IN RAW DEFAULT NULL) 
  RETURN RAW;
```

CONCAT函数用于将最多12个RAW连接到一个RAW中。如果连接的大小超过8000，则返回错误。

|  参数| 描述|
| :---- | :---------------------------------- |
| r1  | RAW类型的值，允许为''或NULL（可选） |
| r2  | RAW类型的值，允许为''或NULL（可选） |
| r3  | RAW类型的值，允许为''或NULL（可选） |
| r4  | RAW类型的值，允许为''或NULL（可选） |
| r5  | RAW类型的值，允许为''或NULL（可选） |
| r6  | RAW类型的值，允许为''或NULL（可选） |
| r7  | RAW类型的值，允许为''或NULL（可选） |
| r8  | RAW类型的值，允许为''或NULL（可选） |
| r9  | RAW类型的值，允许为''或NULL（可选） |
| r10 | RAW类型的值，允许为''或NULL（可选） |
| r11 | RAW类型的值，允许为''或NULL（可选） |
| r12 | RAW类型的值，允许为''或NULL（可选） |

函数使用规则：

- 将0-12个raw的集合合并为一个raw，如果总大小超过8000则报错。
- 输入''或者null则跳过该参数继续合并。

```plsql
SELECT utl_raw.concat() FROM dual;

UTL_RAW.CONCAT()                                                 
---------------------------------------------------------------- 


SELECT utl_raw.concat('','') FROM dual;

UTL_RAW.CONCAT('',''                                             
---------------------------------------------------------------- 


SELECT utl_raw.concat('01','02') FROM dual;

UTL_RAW.CONCAT('01',                                             
---------------------------------------------------------------- 
0102                                                            

```

## COPIES

```plsql
UTL_RAW.COPIES (
   r IN RAW,
   n IN NUMBER) 
  RETURN RAW;
```

COPIES函数用于返回串联在一起的r的n个副本。

|  参数| 描述|
| :--- | :----------------------------- |
| r  | RAW类型的值，允许为''或NULL    |
| n  | NUMBER类型的值，复制的副本数量 |

函数使用规则：

- 获取raw的n倍，n必须为正数。
- r为null 或为''，n < 0.5均会报错，超过最大长度。

```plsql
SELECT utl_raw.copies('ff01', 0) FROM dual;

[1:31]YAS-05213 invalid argument

SELECT utl_raw.copies('ff01', 1) FROM dual;

UTL_RAW.COPIES('FF01                                             
---------------------------------------------------------------- 
FF01                                                            

```

## LENGTH

```plsql
UTL_RAW.LENGTH (
   r  IN RAW) 
RETURN NUMBER;
```

LENGTH函数用于返回RAW r的字节长度。

|  参数| 描述|
| :--- | :-------------------------- |
| r  | RAW类型的值，允许为''或NULL |

函数使用规则：

- 获取raw的字节，返回值为number。
- 输入''或null返回null。

```plsql
SELECT utl_raw.length('') FROM dual;

UTL_RAW.LENGTH('') 
------------------ 
                 0


SELECT utl_raw.length() FROM dual;

[1:8]YAS-04323 arguments count must be 1
```

## REVERSE

```plsql
UTL_RAW.REVERSE (
   r IN RAW) 
  RETURN RAW;
```

REVERSE函数用于将RAW r中的字节序列从一端反转到另一端。

|  参数| 描述|
| :--- | :---------------------------- |
| r  | RAW类型的值，不允许为''或NULL |

函数使用规则：

- 按字节倒置raw。
- 输入为null或''报错。

```plsql
SELECT utl_raw.REVERSE('1234') FROM dual;

UTL_RAW.REVERSE('123                                             
---------------------------------------------------------------- 
3412                                                            

```

## SUBSTR

```plsql
UTL_RAW.SUBSTR (
   r   IN RAW,
   pos IN BINARY_INTEGER,
   len IN BINARY_INTEGER DEFAULT NULL) 
  RETURN RAW;
```

SUBSTR函数用于返回len字节的RAW，从RAW r的pos开始。

|  参数| 描述|
| :---- | :------------------- |
| r   | RAW类型的值，不允许为''或NULL |
| pos | BINARY_INTEGER类型的值，截取raw的起始位置 |
| len | BINARY_INTEGER类型的值（可选），截取的字节长度 |

函数使用规则：

- 获取raw中的部分字节，从pos开始，共计len个字节（不超过末尾）。
- pos为正，从头部开始计算，从1开始，0等价于1，小数向下取整（不考虑符号位）。
- pos为负，从尾部开始计算。
- len默认直到末尾，但不能为0，小数向下取整。
- r输入'' 或者 null则报错。

```plsql
SELECT utl_raw.substr('01020304050607', -7, 7) FROM dual;

UTL_RAW.SUBSTR('0102                                             
---------------------------------------------------------------- 
01020304050607                                                  


SELECT utl_raw.substr('01020304050607', -7, 0) FROM dual;

[1:45]YAS-05213 invalid argument

SELECT utl_raw.substr('01020304050607', 0) FROM dual;

UTL_RAW.SUBSTR('0102                                             
---------------------------------------------------------------- 
01020304050607                                                  

```

## 异常说明

|  参数| 描述|
| ----------- | ---------- |
| VALUE_ERROR | 输入值错误 |

示例

```plsql
SELECT utl_raw.cast_to_number('35533922395E405C2E5556101010101010101010101') AS a FROM dual;

YAS-04412 value error, out of range [0, 100)
```
