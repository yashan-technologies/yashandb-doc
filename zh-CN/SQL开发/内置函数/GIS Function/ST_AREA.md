```ebnf+diagram
st_area::= ST_AREA "(" geometry ")"
```

ST_AREA函数用于计算geometry的面积，或者说计算的是区域的面积，对于不能构成区域的几何图形，则返回0。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，遵循如下规则：

* geometry是POLYGON、MULTIPOLYGON或GEOMETRY COLLECTION类型会返回对应的面积，其它的geometry类型会返回0。
* geometry的空间参考系标识号（SRID）必须在spatial_ref_sys系统表中定义或者为0，否则报错。
* geometry的空间参考系标识号（SRID）在spatial_ref_sys中对应的srs_type如果是GEOGRAPHY2D或者GEOGRAPHY3D，会切换到大地坐标算法计算，否则会使用投影坐标算法。

本函数遵守如下规则：

* 当输入的参数存在NULL时，函数返回NULL。
* 该函数只会计算2D结果，如果输入的是三维，则会忽略Z坐标进行计算。
* 对于输入的经纬度坐标，如果输入的数值不在有效经纬度范围内，则会转换成有效经纬度进行计算。

示例（HEAP表）

```sql
SELECT ST_Area(ST_GeomFromText('POLYGON ((-71.17 42.39,-72.17 43.39,-72.17 44.39,-71.17 42.39))', 4326)) res FROM dual;

RES
----------- 
4.575E+009

SELECT ST_Area(ST_GeomFromText('POLYGON((0 0, 10 0, 10 10, 0 10, 0 0))', 3385)) res FROM dual;

RES
----------- 
1.0E+002
```
