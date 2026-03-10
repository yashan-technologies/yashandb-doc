```ebnf+diagram
timestamp_to_scn::= TIMESTAMP_TO_SCN "(" expr ")"
```

TIMESTAMP_TO_SCN函数将[expr](../通用SQL语法/expr)表示的时间戳数据转换为SCN号。
    
expr的值须为TIMESTAMP类型，时区类型或者符合TIMESTAMP格式的字符型，否则返回类型不支持或格式转换错误。

expr不能为NULL。

示例

```sql
SHOW PARAMETER DATE_FORMAT

NAME                                  VALUE
------------------------------------- --------------------------------------
DATE_FORMAT                           YYYY-MM-DD HH24:MI:SS

-- SYSDATE函数的返回格式取决于DATE_FORMAT参数配置

SELECT SYSDATE,TIMESTAMP_TO_SCN(TO_CHAR(SYSDATE)) scn1,SYSTIMESTAMP,TIMESTAMP_TO_SCN(SYSTIMESTAMP) scn2 FROM DUAL;

SYSDATE                                           SCN1 SYSTIMESTAMP                                                 SCN2
-------------------------------- --------------------- -------------------------------------------- --------------------
2025-07-02 11:11:54                 711021010944000000 2025-07-02 11:11:54.405516 +08:00              711021012604993536
```
