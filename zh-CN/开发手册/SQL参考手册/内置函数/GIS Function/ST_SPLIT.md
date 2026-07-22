```ebnf
st_split = ST_SPLIT "(" input "," blade ")".
```

ST_SPLIT函数用于返回input几何对象被blade几何对象切割之后产生的几何对象。

本函数遵守如下规则：

- 输入参数input和blade须具有相同的空间参考系，否则函数返回错误。
- 输入参数input和blade任意一个为NULL时，函数返回NULL。
- 输入参数input和blade任意一个的坐标中包含NAN和INF时，函数返回错误。
- 输入参数input只能为LineString、MultiLineString、Polygon、MultiPolygon、GeometryCollection。
  - input为LineString时，blade可以为Point、MultiPoint、LineString、MultiLineString、Polygon或MultiPolygon。
  - input为Polygon时，blade只能为MultiLineString和LineString。
  - input为GeometryCollection时，会依次对GeometryCollection中的每个成员做切割，如果其中一个成员不支持切割，返回错误。

**input**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**blade**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

示例（HEAP表）

```sql
select st_astext(st_split(st_geomfromtext('linestring(1 1, 3 3)'), st_geomfromtext('point(2 2)')), 0) split from dual;

SPLIT
----------------------------------------------------------------
GEOMETRYCOLLECTION (LINESTRING (1 1, 2 2), LINESTRING (2 2, 3 3))

select st_astext(st_split(st_geomfromtext('linestring(1 1, 4 4)'), st_geomfromtext('multipoint(2 2, 3 3)')), 0) split from dual;

SPLIT
----------------------------------------------------------------
GEOMETRYCOLLECTION (LINESTRING (3 3, 4 4), LINESTRING (1 1, 2 2), LINESTRING (2 2, 3 3))

select st_astext(st_split(st_geomfromtext('linestring(1 1, 4 4)'), st_geomfromtext('polygon((0 0, 0 2, 2 2, 2 0, 0 0))')), 0) split from dual;

SPLIT
----------------------------------------------------------------
GEOMETRYCOLLECTION (LINESTRING (1 1, 2 2), LINESTRING (2 2, 4 4))

select st_astext(st_split(st_geomfromtext('polygon((0 0, 0 2, 2 2, 2 0, 0 0))'), st_geomfromtext('linestring(1 -1, 1 3)')), 0) split from dual;

SPLIT
----------------------------------------------------------------
GEOMETRYCOLLECTION (POLYGON ((0 0, 0 2, 1 2, 1 0, 0 0)), POLYGON ((1 2, 2 2, 2 0, 1 0, 1 2)))

select st_astext(st_split(st_geomfromtext('geometrycollection(polygon((0 0, 0 2, 2 2, 2 0, 0 0)), linestring(0 0, 2 2))'), st_geomfromtext('linestring(1 -1, 1 3)')), 0) split from dual;

SPLIT
----------------------------------------------------------------
GEOMETRYCOLLECTION (POLYGON ((0 0, 0 2, 1 2, 1 0, 0 0)), POLYGON ((1 2, 2 2, 2 0, 1 0, 1 2)), LINESTRING (0 0, 1 1), LINESTRING (1 1, 2 2))
```
