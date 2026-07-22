```ebnf
greatest = GREATEST "(" expr {"," expr} ")".
```

The GREATEST function finds the maximum value among multiple parameters [expr](../General SQL Syntax/expr). 



This function performs conversion and calculation according to the following rules in sequence. Once a rule is matched, subsequent rules will not be evaluated:

1. If any expr value in the argument list is NULL, the function returns NULL.
2. When DOUBLE-type data exists in expr, all input parameters are converted to DOUBLE for comparison and return.
3. When expr involves mixed comparisons between FLOAT and DECIMAL, or between FLOAT and any datetime type, comparison and return are performed as DOUBLE.
4. When expr involves mixed comparisons among the following three scenarios, comparison and return are performed as DOUBLE.
    - numeric vs character
    - numeric vs RAW
    - numeric vs TIME
5. In purely numeric comparisons:
    - If the expression contains FLOAT, comparison and return are performed as FLOAT;
    - If FLOAT is absent but DECIMAL is present, comparison and calculation are performed as DECIMAL;
    - If neither FLOAT nor DECIMAL is present, pairwise comparisons of input parameters are performed in the order they appear in expr, according to the following table.

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

6. When RAW-type data exists in expr, all input parameters are converted to RAW for comparison and return.
7. When expr involves purely character-type comparisons, if VARCHAR is present, comparison and return are performed as VARCHAR; otherwise, they are performed as CHAR.
8. When expr involves datetime comparisons, the evaluation rules are as shown in the following table.

    |DATA TYPE      |Rules      |
    | ----------- | -------- | 
    |TIMESTAMP    |1. When comparing with numeric types, if any input parameter is of DECIMAL type, comparison and return are performed as DECIMAL; otherwise, they are performed as BIGINT.<br>When comparing with character types, if VARCHAR is present, comparison and return are performed as VARCHAR; otherwise, they are performed as CHAR.<br>3. When comparing with other datetime types, values are converted to TIMESTAMP for comparison and return. |
    |DATE         |1. When comparing with numeric types, if any input parameter is of DECIMAL type, comparison and return are performed as DECIMAL; If DECIMAL is not present but BIGINT or UBIGINT exists, comparison and return are performed as BIGINT; Otherwise, comparison and return are performed as INT.<br>2. When comparing with character types, if VARCHAR is present, comparison and return are performed as VARCHAR; otherwise, they are performed as CHAR.<br>3. When comparing with TIMESTAMP or TIME types, values are converted to TIMESTAMP for comparison and return.<br>4. When comparing with DATE type, values are handled as DATE.|
    |TIME         |1.  When comparing with character types, if VARCHAR is present, comparison and return are performed as VARCHAR; otherwise, they are performed as CHAR. <br>2. When comparing with TIME type, values are handled as TIME.|



***Example*** for Heap tables

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
