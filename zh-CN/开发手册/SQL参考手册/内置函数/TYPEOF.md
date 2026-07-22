```ebnf
typeof = TYPEOF "(" expr1 ["," expr2] ")".
```

TYPEOF函数获取[expr1](../通用SQL语法/expr)参数的数据类型，将其作为VARCHAR类型的字符串返回。

当expr1的值为NULL时，函数返回varchar。

expr2取值范围为0和1、列存表中不可指定本参数。
- 0表示显示typeof中expr1所有参数类型信息（包括类型精度以及字符串长度）和返回值类型。
- 1表示显示返回值类型以及类型对应的精度或长度信息。

当expr2为空时，typeof函数只显示返回值类型。

示例

```sql
SELECT TYPEOF('') t1,
TYPEOF('1.2345') t2,
TYPEOF(1.2345) t3,
TYPEOF(SYSDATE) t4
FROM DUAL;
T1        T2    T3        T4                                                              
--------- ----- --------- ----------------------------------------------------------------
varchar   char  number    date

SELECT TYPEOF(1.21, 0) RES FROM DUAL;

RES
-----------------------------------------
number(3, 2)
[expr_const:number(3, 2)]

SELECT TYPEOF(1.21, 1) RES FROM DUAL;

RES
-------------
number(3, 2)
```
