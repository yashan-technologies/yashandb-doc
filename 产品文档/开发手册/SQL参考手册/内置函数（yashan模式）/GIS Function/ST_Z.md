```ebnf+diagram
st_z::= ST_Z "(" geometry ")"
```

ST_Z函数用于返回输入的geometry的z轴坐标。

本函数遵守如下规则：

- 当输入的参数存在NULL时，函数返回NULL。 
- 当输入的Geometry类型不是Point时，将返回错误。 
- 当输入的Geometry坐标只有2维时，将返回NULL。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。



示例（HEAP表）

```sql
SELECT ST_Z(ST_GeomFromText('POINT(1 2 3)')) res FROM DUAL;

RES
----------- 
3.0E+000

SELECT ST_Z(ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

        RES
-----------


```