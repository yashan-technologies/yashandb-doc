```ebnf+diagram
st_isempty::= ST_ISEMPTY "(" geometry ")"
```

ST_ISEMPTY函数根据输入的geometry，返回该geometry是否为空，即EMPTY，如该geometry均为EMPTY，则返回TRUE，否则返回FALSE。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

当输入的参数存在NULL时，函数返回NULL，空串作为NULL处理。

示例（HEAP表）

```sql
SELECT ST_IsEmpty(ST_GeomFromText('POLYGON EMPTY')) res FROM DUAL;

RES
-------------------- 
true

SELECT ST_IsEmpty(ST_GeomFromText('POLYGON((1 2, 3 4, 5 6, 1 2))')) res FROM DUAL;

RES
-------------------- 
false               
```
