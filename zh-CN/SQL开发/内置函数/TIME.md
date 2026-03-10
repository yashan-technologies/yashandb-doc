```ebnf+diagram
time::= TIME "(" expr ")"
```

TIME函数用于获取[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)的时间部分数值。

*   当expr的值不为NULL时，函数返回TIME类型。

*   当expr的值为NULL时，函数返回NULL。

**expr**

expr的值须为DATE、TIME、TIMESTAMP类型或可转换为相应时间类型的其他类型数据。

当expr的值为字符型时，其格式必须符合以下规范：

*   字符串类型1：以'yyyy-mm-dd'开头，且至少包含有'yyyy-mm-dd'格式的字符串，需符合年、月、日的一般限制条件，如月份值介于1-12之间、日期值介于1-31之间等。

*   字符串类型2：'hh24:mi:ss.ff'格式的字符串，此字符串可从后向前省略部分，需符合小时、分、秒的一般限制条件，如小时值介于0-23之间、分钟值介于0-59之间等。此时TIME函数会对省略的部分补0。

示例

```sql
SELECT TIME(MAX(SYSDATE)) res FROM DUAL;
RES  
--------------------
16:32:15.000000
 
SELECT TIME('2012-3-18') res FROM DUAL;
RES   
--------------------
00:00:00.000000    
 
SELECT TIME('2012-3-18 14') res FROM DUAL;
RES 
--------------------
14:00:00.000000
```
