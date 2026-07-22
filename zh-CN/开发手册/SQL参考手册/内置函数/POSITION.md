```ebnf
position = POSITION "(" expr1 IN expr2 ")".
```

POSITION函数在一个[expr](../通用SQL语法/expr)2表示的字符串中从左向右查找[expr](../通用SQL语法/expr)1表示的字符串，返回expr1第一次出现的位置，该结果为一个BIGINT类型的数字。若没有查找到匹配值，函数返回0。

- expr1、expr2的值须为字符型或除JSON、LOB、BFILE类型外的可转化为字符型的其他类型。

- 当expr1或expr2中任一值为NULL时，函数返回NULL。

示例

```sql
SELECT employee_name n,
POSITION('a' IN employee_name) p
FROM employees;
N                                 P
------------- ---------------------
Mask                              2
John                              0
Anna                              4
Jack                              2
Jim                               0
```
