```ebnf+diagram
st_transform::= ST_TRANSFORM "(" geometry "," srid ")"
```

```ebnf+diagram
st_transform::= ST_TRANSFORM "(" geometry "," from_proj "," srid ")"
```

ST_TRANSFORM函数根据输入的geometry和srid，返回geometry从原本的空间参考系转换到srid所指定的空间参考系的坐标数据的新geometry。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

*   必须有srid并且srid必须在系统表spatial_ref_sys中定义，否则会报错。

**from_proj**

字符串源空间参考。

**srid**

srid的数据类型是INT，表示输出结果中的空间参考系，遵循如下规则：

*   支持能够隐式转换成INT的类型，如输入小数则进行四舍五入转换。
*   必须在系统表spatial_ref_sys中定义，否则会报错。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT ST_AsText(ST_Transform(ST_GeomFromText('polygon((73.62 16.7, 74.15 16.45, 73.77 16.32, 73.62 16.7))', 4326), 4491),2) res FROM dual;

RES                                             
---------------------------------------------------------------- 
POLYGON ((13352805.80 1847616.32, 13409224.37 1819631.38, 13368550.72 1805451.07, 13352805.80 1847616.32))

SELECT ST_AsText(ST_Transform(ST_GeomFromText('linestring(73.62 16.7, 74.15 16.45)', 4326), 4491),2) res FROM dual;

RES                                             
---------------------------------------------------------------- 
LINESTRING (13352805.80 1847616.32, 13409224.37 1819631.38)
```
