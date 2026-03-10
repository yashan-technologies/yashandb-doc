```ebnf+diagram
st_x::= ST_X "(" geometry ")"
```

ST_X函数根据输入的geometry，返回该点的x轴坐标。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

当输入的参数存在NULL时，函数返回NULL，空串作为NULL处理。

示例（HEAP表）

```sql
SELECT ST_X(ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

RES
----------- 
1.0E+000
```
