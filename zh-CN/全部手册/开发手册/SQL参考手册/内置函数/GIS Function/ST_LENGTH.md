```ebnf+diagram
st_length::= ST_LENGTH "(" geometry ")"
```

ST_LENGTH函数根据输入的geometry，返回对应的长度数据。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，遵循如下规则：

* geometry是LINESTRING、MULTILINESTRING或GEOMETRY COLLECTION类型会返回对应的长度，其它的geometry类型会返回0。
* geometry的空间参考系标识号（SRID）必须在spatial_ref_sys系统表中定义或者为0，否则报错。
* geometry的空间参考系标识号（SRID）在spatial_ref_sys中对应的srs_type如果是GEOGRAPHY2D或者GEOGRAPHY3D，会切换到大地坐标算法计算，否则会使用投影坐标算法。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT ST_Length(ST_GeomFromText('linestring(-72.1235 42.3521, -72.1523 42.6343)', 4326)) res FROM dual;

        RES
-----------
 3.144E+004

SELECT ST_Length(ST_GeomFromText('linestring(5000 6789, 12345 5789)', 3385)) res FROM dual;

        RES
-----------
 7.413E+003
```
