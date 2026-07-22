DBMS_ASSERT包提供了一组内置的存储过程/函数，用于验证输入值的属性。

> **Caution**:
>
> - DBMS_ASSERT高级包不适用于存算一体分布式集群部署。

## ENQUOTE_LITERAL

```plsql
DBMS_ASSERT.ENQUOTE_LITERAL(str VARCHAR)RETURN VARCHAR
```

该函数将使用单引号转义输入字符串并返回转义后的字符串，具体转义规则为：

- 验证输入字符串中单引号是否与相邻的单引号配对，不配对则会报错。

- 检查输入的字符串中是否已被单引号转义，若是则不会重复转义，直接返回原数据。

|  参数| 描述|
| -------- | ------------------------------------------------------------ |
|STR|需要转义的字符串|

示例（单机/共享集群/分布式集群部署）

```sql
SQL> SELECT DBMS_ASSERT.ENQUOTE_LITERAL('TEST') FROM DUAL;

DBMS_ASSERT.ENQUOTE_LITERAL('TEST')                              
---------------------------------------------------------------- 
'TEST'                                                          

1 row fetched.
```
