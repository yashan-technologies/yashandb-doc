```ebnf
greatest = GREATEST "(" expr {"," expr} ")".
```

GREATEST从多个参数[expr](../通用SQL语法/expr)的值中找出最大的一个值。



本函数按以下规则顺序进行转换和计算，当命中某个规则时不再对后续规则进行判断：

1. 当参数列表中的任一个expr值为NULL时，函数返回NULL。
2. 当expr中存在DOUBLE类型数据时，将所有入参转换为DOUBLE进行比较与返回。
3. 当expr中存在FLOAT与DECIMAL混合比较，或FLOAT与任意日期时间类型混合比较时，按照DOUBLE类型进行比较与返回。
4. 当expr中存在以下三种混合场景比较时，按照DOUBLE类型进行比较与返回。
    - 数值与字符
    - 数值与RAW
    - 数值与TIME类型
5. 纯数值型比较时:
    - 如果表达式中存在FLOAT类型，则按照FLOAT进行比较与返回；
    - 如果不存在FLOAT类型但存在DECIMAL类型，则按照DECIMAL类型进行比较计算；
    - 如果FLOAT和DECIMAL类型都不存在，按expr顺序进行入参的两两比较，规则如下表格所示。

    | expr1\expr2 | TINYINT  | SMALLINT | INT    | BIGINT | UTINYINT  | USMALLINT | UINT    | UBIGINT |
    | ----------- | -------- | -------- | ------ | ------ | --------- | --------- | ------- | ------- |
    | TINYINT     | TINYINT  | SMALLINT | INT    | BIGINT | SMALLINT  | INT       | BIGINT  | NUMBER  |
    | SMALLINT    | SMALLINT | SMALLINT | INT    | BIGINT | SMALLINT  | INT       | BIGINT  | NUMBER  |
    | INT         | INT      | INT      | INT    | BIGINT | INT       | INT       | BIGINT  | NUMBER  |
    | BIGINT      | BIGINT   | BIGINT   | BIGINT | BIGINT | BIGINT    | BIGINT    | BIGINT  | NUMBER  |
    | UTINYINT    | SMALLINT | SMALLINT | INT    | BIGINT | UTINYINT  | USMALLINT | UINT    | UBIGINT |
    | USMALLINT   | INT      | INT      | INT    | BIGINT | USMALLINT | USMALLINT | UINT    | UBIGINT |
    | UINT        | BIGINT   | BIGINT   | BIGINT | BIGINT | UINT      | UINT      | UINT    | UBIGINT |
    | UBIGINT     | NUMBER   | NUMBER   | NUMBER | NUMBER | UBIGINT   | UBIGINT   | UBIGINT | UBIGINT |

6. 当expr中存在RAW类型数据时，将所有入参转换为RAW进行比较与返回。
7. 当expr为纯字符类型比较，存在VARCHAR时则按VARCHAR比较与返回，否则按CHAR比较与返回。
8. 当expr涉及日期时间比较时，判断规则如下表格所示。

    |数据类型|规则|
    | ----------- | -------- | 
    |TIMESTAMP    |1. 与数值类型比较时，如果入参有DECIMAL类型，则按DECIMAL进行比较与返回，否则按照BIGINT类型进行比较与返回；<br>2. 与字符类型比较时，存在VARCHAR时则按VARCHAR比较与返回，否则按CHAR比较与返回；<br>3. 与其他日期时间类型比较时，转为TIMESTAMP进行比较并返回。 |
    |DATE         |1. 与数值类型比较时，如果入参有DECIMAL类型，则按DECIMAL进行比较与返回；不存在DECIMAL类型但存在BIGINT或UBIGINT类型时，按照BIGINT类型进行比较与返回；否则按照INT类型进行比较与返回<br>2. 与字符类型比较时，存在VARCHAR时则按VARCHAR比较与返回，否则按CHAR比较与返回；<br>3. 与TIMESTAMP、TIME类型比较时，转为TIMESTAMP进行比较并返回;<br>4. 与DATE类型比较时，按照DATE类型处理。|
    |TIME         |1. 与字符类型比较时，存在VARCHAR时则按VARCHAR比较与返回，否则按CHAR比较与返回；<br>2. 与TIME类型比较时，按照TIME类型处理。|



示例（HEAP表）

```sql
create table t1(tim time, nm number);
insert into t1 values('02:00:01', 5.3);

SELECT GREATEST(2, 5, 12, 3, '16', 8, 9) AS RES1,
GREATEST(6, 7, 5000, 0x32, 5.7) AS RES2,
GREATEST(2008, tim) AS RES3,
GREATEST(timestamp'2008-02-01 00:01:00', nm) as RES4,
GREATEST(timestamp'2008-02-01 00:01:00', '2008-02-01') as res5,
GREATEST(timestamp'2008-02-01 00:01:00', date'2008-02-01') as res6
FROM t1;

       RES1        RES2        RES3        RES4 res5                  res6
----------- ----------- ----------- ----------- --------------------- ----------------------------------------------------------------
   1.6E+001    5.0E+003    2.0E+004  2.0080E+13 2008-02-01 00:01:00   2008-02-01 00:01:00.000000

SELECT GREATEST(2, 5, 12, 3, 16, 8, 9) AS RES1,
GREATEST('100', 6, 7, 5000, '3.5', 5.7) AS RES2,
GREATEST(sysdate,TO_DATE('2014-08-01','YYYY-MM-DD')) AS RES3,
GREATEST(true,false) RES4,
GREATEST('A', '1', 'b') as res5
FROM DUAL;

        RES1        RES2 RES3                                                              RES4 res5
------------ ----------- ---------------------------------------------------------------- ----- -----
          16    5.0E+003 2025-12-25 14:53:42.000000                                           1 b

SELECT GREATEST('1',date'2002-01-01');

YAS-00008 type convert error : incorrect datetime value

SELECT GREATEST('1',timestamp'2002-01-01 00:01:00');

YAS-00008 type convert error : incorrect datetime value
```
