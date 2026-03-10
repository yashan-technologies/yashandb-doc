```ebnf+diagram
chartorowid::= CHARTOROWID "(" char ")"
```

CHARTOROWID用于将字符型、CLOB、NCLOB、RAW类型的参数转换为ROWID类型。

当char为NULL时函数返回NULL。

char必须符合ROWID类型的合法格式要求。

示例（HEAP表）

```sql
SELECT CHARTOROWID('2368:0:0:3164:0') rowid1,TYPEOF(CHARTOROWID('2368:0:0:3164:0')) type1 FROM DUAL;
ROWID1                     TYPE1
-------------------------- ---------
2368:0:0:3164:0            rowid
```
