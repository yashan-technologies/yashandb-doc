```ebnf+diagram
numtodsinterval::= NUMTODSINTERVAL "(" expr "," "'"(DAY|HOUR|MINUTE|SECOND)"'" ")"
```

NUMTODSINTERVAL函数将[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)表示的除BIT外数值型数据转换为以DAY|HOUR|MINUTE|SECOND为单位的INTERVAL DAY TO SECOND类型的数值。

当expr的值为NULL时，返回NULL。

当expr的值为非数值型数据时，将先进行到NUMBER类型的转换，转换失败时返回Invalid number错误。

当指定DAY|HOUR|MINUTE|SECOND单位时，对DAY|HOUR|MINUTE|SECOND的大小写不敏感。

示例

```sql
SELECT NUMTODSINTERVAL('4','minute') res FROM DUAL;
RES            
--------------------------------
+00 00:04:00.000000            
 
--获取在当前时间后四分钟的时间值
SELECT SYSDATE, SYSDATE+NUMTODSINTERVAL('4','minute') sysdate2 FROM DUAL;
SYSDATE                          SYSDATE2                                  
-------------------------------- --------------------------------
2021-11-30 17:03:42              2021-11-30 17:07:42
```
