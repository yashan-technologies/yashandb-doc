```ebnf+diagram
MONTHS_BETWEEN::= MONTHS_BETWEEN "(" expr1 "," expr2 ")" 
```

MONTHS_BETWEEN函数用于计算expr1和expr2之间间隔的满月月份数，和不满月的小数部分（expr1-expr2），返回一个NUMBER类型的数值。

expr1和expr2为YashanDB认可的[通用表达式](../通用SQL语法/expr)，其值须为DATE/TIMESTAMP类型或可以转换为DATE/TIMESTAMP的字符型。

expr1或者expr2为NULL时，函数返回NULL。

示例

```sql
--日期相同，计算结果为整数
SELECT MONTHS_BETWEEN('2021-03-13', '2021-02-13') res FROM DUAL;
        RES 
----------- 
          1
               
--均为所在月最后一天，计算结果为整数              
SELECT MONTHS_BETWEEN('2021-04-30', '2021-01-31') res FROM DUAL;
        RES 
----------- 
          3

--间隔不为整月，计算结果带小数               
SELECT MONTHS_BETWEEN('2021-03-01', '2021-02-28') res FROM DUAL;
        RES 
----------- 
 .129032258

--间隔不为整月，计算结果带小数
SELECT MONTHS_BETWEEN('2021-04-01', '2021-02-28') res FROM DUAL;
        RES 
----------- 
 1.12903226
```

