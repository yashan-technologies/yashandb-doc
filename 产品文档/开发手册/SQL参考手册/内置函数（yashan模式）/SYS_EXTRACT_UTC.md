```ebnf+diagram
sys_extract_utc::= SYS_EXTRACT_UTC "(" datetime ")"
```

SYS_EXTRACT_UTC函数主要用于将输入的datetime转换成UTC（原格林尼治标准时间）对应的时间并返回一个Timestamp类型数据。

本函数遵循如下规则：

- datetime须为Timestamp或者时区类型。

示例

```sql
SELECT SYS_EXTRACT_UTC(TIMESTAMP'2000-02-01 4:00:00') res FROM dual;

RES
----------------------------------------------------------------
2000-01-31 20:00:00.000000

```