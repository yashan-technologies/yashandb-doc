```ebnf+diagram
to_yminterval::= TO_YMINTERVAL "(" "'" ym_sql_format "'" ")" 

ym_sql_format::= (["+"|"-"] years "-" months)
```

TO_YMINTERVAL函数将[expr](../通用SQL语法/expr)的值转换为INTERVAL YEAR TO MONTH类型的数值。

当expr的值为NULL时，函数返回NULL。

expr的值应该为字符型，否则返回类型不支持。且该字符串需符合ym_sql_format格式要求，否则返回格式转换错误。

**ym_sql_format**

与SQL标准（ISO/IEC 9075）兼容的SQL间隔格式，需要声明years和months两个元素，years与months间用减号隔开，且允许存在额外的空格，例如`'5-  2'`。

- years：整数，取值范围为[-178000000,178000000]。

- months：整数，取值范围为[0,11]。

示例

```sql
SELECT TO_YMINTERVAL('5-2') res FROM DUAL;
RES
--------------------
+05-02  
 
--查询在当前日期前五年的时间值
SELECT SYSDATE+TO_YMINTERVAL('-5-0') res FROM DUAL;
RES            
--------------------------------
2016-11-29 22:51:41
```
