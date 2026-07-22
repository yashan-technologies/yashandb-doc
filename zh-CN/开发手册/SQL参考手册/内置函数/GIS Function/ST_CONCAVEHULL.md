```ebnf
st_concavehull = ST_CONCAVEHULL"(" geometry ", "ratio"[, "allow_holes"])".
```

ST_CONCAVEHULL函数用于计算一个Geometry对象的凹包。凹包指可以覆盖输入的几何对象所有顶点的一个几何对象，该几何对象一般为一个凹多边形。

本函数遵守如下规则：

- 输入为一个点或多个相同的点，返回结果仍为一个点。
- 输入为多个共线的点，返回结果将为一个线段。
- 输入为共线的两个或多个线段，返回结果将为一个线段。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**ratio**

[通用表达式](../../通用SQL语法/expr)，用于控制最短与最长边的长度比，从而影响凹包的凹度，类型为DOUBLE，有效范围为[0,1.0]。

- 凹包的构建过程采用Delaunay三角剖分的原理，通过不断地移除最长的外边来达到最优的结果。
- 当ratio为1时，返回结果为凸包；当ratio为0时，返回的结果为凹度最大的凹包；当ratio介于0和1之间时，ratio越大，凹度越大。

**allow_holes**

[通用表达式](../../通用SQL语法/expr)，该参数类型为bool，缺省值为false，表示是否允许返回的多边形中包含洞。

示例（HEAP表）

```sql
select st_astext(st_concavehull(st_geomfromtext('point(0 0)'), 1, 0), 0) from dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
POINT (0 0)

select st_astext(st_concavehull(st_geomfromtext('multipoint(0 0, 1 1)'), 1, 0), 0) from dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
LINESTRING (0 0, 1 1)

select st_astext(st_concavehull(st_geomfromtext('multipoint(1 1, 1 1)'), 1, 0), 0) from dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
POINT (1 1)

select st_astext(st_concavehull(st_geomfromtext('linestring(0 0, 1 0, 1 1)'), 1, 0), 0) from dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
POLYGON ((0 0, 1 1, 1 0, 0 0))

select st_astext(st_concavehull(st_geomfromtext('geometrycollection(multipoint(0 0, 1 1), multipoint(2 2, 3 3))'), 1, 0), 0) from dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
LINESTRING (0 0, 3 3)

select st_astext(st_concavehull(st_geomfromtext('geometrycollection(linestring(0 0, 1 1), linestring(2 2, 3 3))'), 1, 0), 0) from dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
LINESTRING (0 0, 3 3)

select st_astext(st_concavehull(st_geomfromtext('geometrycollection(polygon((0 0, 1 0, 1 1, 0 1, 0 0)), polygon((10 10, 20 10, 20 20, 10 20, 10 10)))'), 1, 0), 0) from dual;

ST_ASTEXT(ST_CONCAVE
----------------------------------------------------------------
POLYGON ((1 0, 0 0, 0 1, 10 20, 20 20, 20 10, 1 0))
```
