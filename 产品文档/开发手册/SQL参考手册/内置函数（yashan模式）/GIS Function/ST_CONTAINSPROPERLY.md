```ebnf+diagram
st_containsproperly::= ST_CONTAINSPROPERLY "(" geometry1 "," geometry2 ")"
```

ST_CONTAINSPROPERLY函数的功能是判断geometry1是否完全包含geometry2，完全包含时返回TRUE，否则返回FALSE。与[ST_CONTAINS](../../内置函数（yashan模式）/GIS Function/ST_CONTAINS)不同的是，ST_CONTAINS(A,A) = TRUE，ST_CONTAINSPROPERLY(A,A) = FALSE。

geometry1完全包含geometry2指geometry2中不存在位于geometry1外部及其边界的点，即geometry2中的所有点都在geometry1内部。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

本函数遵守如下规则：

* 当输入的参数存在NULL时，函数返回NULL。
* 若输入的任意一个geometry为EMPTY，函数返回FALSE。
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。
* 结果精度为小数点后15位，超出部分的精度不作保证。
* 遵循DE-9IM（Dimensionally Extended 9-Intersection Model）规则。
* 该函数支持使用RTREE索引。

示例（HEAP表）

```sql
--POLYGON完全包含LINESTRING中的点
SELECT ST_ContainsProperly(ST_GeomFromText('POLYGON((0 0, 4 0, 4 4, 0 4, 0 0))'), ST_GeomFromText('LINESTRING(3 3, 2 3)')) res FROM DUAL;

RES
-------------------- 
true                
       
--LINESTRING有一个点与POLYGON相交
SELECT ST_ContainsProperly(ST_GeomFromText('POLYGON((0 0, 4 0, 4 4, 0 4, 0 0))'), ST_GeomFromText('LINESTRING(3 3, 4 4)')) res FROM DUAL;

RES
-------------------- 
false

--输入存在NULL
SELECT ST_ContainsProperly(ST_GeomFromText('POLYGON((0 0, 4 0, 4 4, 0 4, 0 0))'), NULL) res FROM DUAL;

RES
--------------------

```

