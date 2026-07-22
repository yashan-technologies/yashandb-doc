```ebnf
st_linemerge = ST_LINEMERGE "(" geometry "[,"directed"])".
```

ST_LINEMERGE函数用于将输入的MultiLineString中的线组合成一个LineString或MultiLineString。

本函数遵守如下规则：

- 输入参数为Point、MultiPoint、Polygon或MultiPolygon时，函数返回GeometryCollection Empty。
- 输入参数为NULL时，函数返回NULL。
- 输入参数为空的LineString或MultiLineString时，函数返回LineString Empty或MultiLineString Empty。
- 输入参数为点、多点时，返回NULL。
- 仅当输入的MultiLineString中两条线相交且交点的度为2（即端点处相交）时，两条线才会合并。
- 函数返回的几何对象，其SRID与输入的几何对象一致。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**directed**

[通用表达式](../../通用SQL语法/expr)，其类型为bool，缺省值为false。当directed为true时，输入的LineString（或MultiLineString中包含的LineString）被视为有向线，否则视为无向。方向相反的有向线无法组合。

示例（HEAP表）

```sql
--directed = true时，两个方向相反的线，端点处相交，但不会合并
select st_astext(st_lineMerge(st_geomfromtext('multilinestring((0 0, 1 1), (2 2, 1 1))'), true), 0) st_linemerge from dual;

ST_LINEMERGE
----------------------------------------------------------------
MULTILINESTRING ((0 0, 1 1), (2 2, 1 1))

--directed = false时，两个方向相反的线，端点处相交，会合并
select st_astext(st_lineMerge(st_geomfromtext('multilinestring((0 0, 1 1), (2 2, 1 1))'), false), 0) st_linemerge from dual;

ST_LINEMERGE
----------------------------------------------------------------
LINESTRING (0 0, 1 1, 2 2)

--两条线的交点（2 0), 是第一条线内部的点，相交之后（2 0）的度为3，因此不会合并
select st_astext(st_lineMerge(st_geomfromtext('multilinestring((0 0, 2 0, 4 0), (2 0, 2 1))')), 0) st_linemerge from dual;

ST_LINEMERGE
----------------------------------------------------------------
MULTILINESTRING ((0 0, 2 0, 4 0), (2 0, 2 1))

--三条线在(1 0)处相交，导致该点的度为3，因此该点不能作为交点，三条线不会合并
select st_astext(st_lineMerge(st_geomfromtext('multilinestring((0 0, 1 0), (1 0, 2 0), (1 0, 1 1))')), 0) st_linemerge from dual;

ST_LINEMERGE
----------------------------------------------------------------
MULTILINESTRING ((0 0, 1 0), (1 0, 2 0), (1 0, 1 1))
```
