```ebnf
st_buildarea = ST_BUILDAREA "(" geometry ")".
```

ST_BUILDAREA函数用于将输入的geometry中的线条组合成一个多边形。

本函数遵守如下规则：

- 本函数针对二维空间对象，当输入的几何对象是三维对象时，输出结果仍为三维对象，但第三维坐标不参与计算。即计算过程中判断线圈是否闭合、是否存在空间包含关系时，只有前二维坐标参与计算，二维空间上闭合但三维空间不闭合的线圈，仍视为闭合。
- 输入参数为NULL时，函数返回NULL。
- 输入参数为空的几何对象时，函数返回一个空的多边形。
- 输入参数为点、多点时，返回NULL。
- 输入参数为一个LineString时，如果LineString为一个首尾闭合的线圈，则返回一个多边形，否则返回NULL。
- 输入参数为一个多边形时，返回多边形。
- 输入参数为集合对象（MultiLineString、MultiPolygon、GeometryCollection）时，函数的表现如下：
  - 如果某个成员单独执行ST_BuildArea返回NULL，则该成员不对结果产生影响。
  - 如果多个成员之间存在包含关系（例如一个线圈包含另外一个线圈，或一个多边形包含另一个多边形），则被包含的对象将被视为多边形的洞。
- 返回的几何对象，其SRID与输入的几何对象一致。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

示例（HEAP表）

```sql
--不闭合的线圈，返回NULL
select st_astext(st_buildarea(st_geomfromtext('linestring(0 0, 4 0, 4 4, 0 4)')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------

--闭合线圈返回由线圈构成的多边形
select st_astext(st_buildarea(st_geomfromtext('linestring(0 0, 4 0, 4 4, 0 4, 0 0)')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------
POLYGON ((0 0, 0 4, 4 4, 4 0, 0 0))

select st_astext(st_buildarea(st_geomfromtext('polygon((0 0, 4 0, 4 4, 0 4, 0 0))')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------
POLYGON ((0 0, 0 4, 4 4, 4 0, 0 0))

select st_astext(st_buildarea(st_geomfromtext('polygon((0 0, 4 0, 4 4, 0 4, 0 0),(2 2, 3 2, 3 3, 2 3, 2 2))')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------
POLYGON ((0 0, 0 4, 4 4, 4 0, 0 0), (2 2, 3 2, 3 3, 2 3, 2 2))

--两个线圈存在包含关系，内部线圈围成的区域视为多边形的洞
select st_astext(st_buildarea(st_geomfromtext('multilinestring((0 0, 4 0, 4 4, 0 4, 0 0),(2 2, 3 2, 3 3, 2 3, 2 2))')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------
POLYGON ((0 0, 0 4, 4 4, 4 0, 0 0), (2 2, 3 2, 3 3, 2 3, 2 2))

--二维闭合三维不闭合的线圈，仍视为闭合，输出结果仍包含三维坐标
select st_astext(st_buildarea(st_geomfromtext('linestring(0 0 0, 4 0 0, 4 4 0, 0 4 0, 0 0 1)')), 0) from dual;

ST_ASTEXT(ST_BUILDAR
----------------------------------------------------------------
POLYGON Z ((0 0 1, 0 4 0, 4 4 0, 4 0 0, 0 0 0))
```
