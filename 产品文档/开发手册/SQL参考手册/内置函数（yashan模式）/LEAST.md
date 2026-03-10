```ebnf+diagram
least::= LEAST "(" (expr) {"," (expr)} ")"
```

LEAST从多个[expr](../通用SQL语法/expr)参数值中找出最小的一个值，并按第一个参数的数据类型返回结果。其规则为：

*   当expr值全为数值型数据时，按大小进行比较。
*   当第一个expr值为数值型数据，且存在一部分为字符型数据时，将字符型转换为NUMBER类型后执行比较，如转换失败则返回Invalid number错误。
*   当expr值全为字符型数据时，按字典序进行比较。
*   当第一个expr值为字符型数据，且存在一部分为非字符型数据时，会把非字符型转换成字符型，然后按字典序进行比较。
*   当expr值全为日期时间型数据时，按照时间大小进行比较。
*   当expr值全为布尔型数据时，按照true=1，false=0进行比较。
*   当参数列表中的任一个expr值为NULL时，函数返回NULL。
*   当expr为LOB、XMLTYPE、JSON时，函数返回unexpected lob错误。
*   上述之外的其他情形，函数返回Invalid datatype错误。

  

示例

```sql
SELECT LEAST(2, 5, 12, 3, 16, 8, 9) AS RES1,
LEAST('A', 6, 7, 5000, 'E', 'F','G') AS RES2,
LEAST(SYSDATE,TO_DATE('2014-08-01','YYYY-MM-DD')) AS RES3,
LEAST(true,false) RES4
FROM DUAL;
        RES1 RES2  RES3                             RES4                
------------ ----- -------------------------------- --------------------
           2 5000  2014-08-01 00:00:00              false
```
