```ebnf
st_distance = ST_DISTANCE "(" geometry1 "," geometry2 ")".
```

ST_DISTANCE函数根据输入的geometry1和geometry2，返回它们对应的距离数据。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，遵循如下规则：

* geometry1和geometry2的空间参考系标识号（SRID）必须相等，否则报错。
* geometry1和geometry2的空间参考系标识号（SRID）必须在spatial_ref_sys系统表中定义或者为0，否则报错。
* geometry1和geometry2的空间参考系标识号（SRID）在spatial_ref_sys中对应的srs_type如果是GEOGRAPHY2D或者GEOGRAPHY3D，会切换到大地坐标算法计算，否则会使用投影坐标算法。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
select ST_Distance(ST_GeomFromText('linestring(-72.1523 42.6343, -72.4524 42.2872)', 4326), ST_GeomFromText('linestring(-72.4524 42.4526, -72.1235 42.3521)',4326)) res from dual;

RES 
------------------------- 
                        0

select ST_Distance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.1244 42.3527, -72.1260 42.45))',4326)) res from dual;

RES 
------------------------- 
  7.3801729438882347E+001

select ST_Distance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.12 42.35, -72.1260 42.45))',4326)) res from dual;

RES 
------------------------- 
                        0

select ST_Distance(ST_GeomFromText('linestring(-72.1235 42.3521, -72.1523 42.6343)', 4326), ST_GeomFromText('linestring(-72.4524 42.4526, -72.4524 42.2872)',4326)) res from dual;

RES 
------------------------- 
  2.6136292567874534E+004

select ST_Distance(ST_GeomFromText('linestring(-72.1235 42.3521, -72.1523 42.6343)', 4490), ST_GeomFromText('linestring(-72.4524 42.4526, -72.4524 42.2872)',4490)) res from dual;

RES 
------------------------- 
   2.613629256806742E+004

select ST_Distance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.12 42.35, -72.1260 42.45))',4326)) res from dual;

RES 
------------------------- 
                        0

select ST_Distance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('linestring(-72.1260 42.45, -72.123 42.1546, -72.12 42.35, -72.1260 42.45)',4326)) res from dual;

RES 
------------------------- 
  1.2380207674721363E+002

select ST_Distance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('multipoint(-72.1260 42.45, -72.123 42.1546, -72.12 42.35, -72.1260 42.45)',4326)) res from dual;

RES 
------------------------- 
  3.7091260963845019E+002
```
