```ebnf+diagram
timestamp_to_scn::= TIMESTAMP_TO_SCN "(" expr ")"
```

TIMESTAMP_TO_SCN函数将[expr](../通用SQL语法/expr)表示的时间戳数据转换为SCN号。
    
expr的值须为TIMESTAMP类型，时区类型或者符合TIMESTAMP格式的字符型，否则返回类型不支持或格式转换错误。

expr不能为NULL。

示例

```sql
SELECT SYSDATE,TIMESTAMP_TO_SCN(TO_CHAR(SYSDATE)) scn1,SYSTIMESTAMP,TIMESTAMP_TO_SCN(SYSTIMESTAMP) scn2 FROM DUAL;
SYSDATE                              SCN1 SYSTIMESTAMP                                 SCN2
--------------------- ------------------- --------------------------- ---------------------
2022-01-09 22:07:08    261527961600000000 2022-01-09 22:07:08.594552     261854120323284992
```