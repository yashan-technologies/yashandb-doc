```ebnf
st_ymax = ST_YMAX "(" expr ")".
```

ST_YMAX函数根据输入的二维或三维空间对象，返回该几何对象的Y轴坐标最大值。

**expr**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型或BOX2D类型的数据。

当前支持输入的ST_GEOMETRY子数据类型为：

- POINT
- LINESTRING
- POLYGON
- MULTIPOINT
- MULTILINESTRING
- MULTIPOLYGON
- GEOMETRYCOLLECTION

当输入的参数为NULL时，函数返回NULL。

示例（HEAP表）

```sql
select ST_YMax(st_geomfromtext('MULTIPOINT Z(1 3  6  , 2   4 8)')) as res from dual ;

        RES 
----------- 
   4.0E+000
```
