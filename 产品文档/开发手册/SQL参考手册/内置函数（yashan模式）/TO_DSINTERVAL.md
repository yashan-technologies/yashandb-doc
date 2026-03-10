```ebnf+diagram
to_dsinterval::= TO_DSINTERVAL "(" "'" sql_format "'" ")" 

sql_format::= (["+" | "-"] days hours ":" minutes ":" seconds ["." frac_secs])
```

TO_DSINTERVAL函数将[expr](../通用SQL语法/expr)的值转换为INTERVAL DAY TO SECOND类型的数值。

当expr的值为NULL时，函数返回NULL。

expr的值应该为字符型数据，否则返回类型不支持。且该字符串需符合sql_format格式要求，否则返回格式转换错误。

**sql_format**

与SQL标准（ISO/IEC 9075）兼容的SQL间隔格式，需要声明days、hours、minutes、seconds和frac_secs五个元素，days和hours间由一个或多个空格隔开，hours、minutes、seconds间由一个冒号隔开。不同元素之间允许存在额外的空格，例如`'23 23    : 23:12'`。

- days：整数，取值范围为[-100000000,100000000]。

- hours：整数，取值范围为[0,23]。

- minutes：整数，取值范围为[0,59]。

- seconds：整数，取值范围为[0,59]。

- frac_secs：小数，取值范围为[.0,.999999]。

示例

```sql
SELECT TO_DSINTERVAL('23 23 : 23:12') res FROM DUAL;
RES            
--------------------------------
+23 23:23:12.000000        
 
--查询在当前日期前两天的时间值
SELECT SYSDATE+TO_DSINTERVAL('-2 00:00:00') res FROM DUAL;
RES            
--------------------------------
2021-11-27 22:32:25
```

