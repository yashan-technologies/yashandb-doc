```ebnf+diagram
st_multi::= ST_Multi "(" geometry ")"
```

ST_MULTI函数用于返回输入的Geometry对象所对应的Geometry Collection类型。若输入的Geometry已是集合类型，则返回输入的Geometry对象。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入Geometry类型与返回Geometry类型的对应关系见下表：

| 输入类型 | 输出类型 |
|--------|----------|
| Point | MultiPoint |
| LineString | MultiLineString|
| Polygon | MultiPolygon |
| MultiPoint | MultiPoint |
| MultiLineString| MultiLineString|
| MultiPolygon | MultiPolygon |
| GeometryCollection | GeometryCollection |

示例（HEAP表）

```sql
SELECT st_astext(st_multi(st_geomfromText('point(0 0)', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTIPOINT (0 0)

SELECT st_astext(st_multi(st_geomfromText('linestring(0 0, 1 1)', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTILINESTRING ((0 0, 1 1))

SELECT st_astext(st_multi(st_geomfromText('polygon((0 0, 1 1, 2 0, 0 0))', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTIPOLYGON (((0 0, 1 1, 2 0, 0 0)))

SELECT st_astext(st_multi(st_geomfromText('multipoint(0 0, 1 1)', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTIPOINT (0 0, 1 1)

SELECT st_astext(st_multi(st_geomfromText('multilinestring((0 0, 1 1),(2 2, 4 4))', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTILINESTRING ((0 0, 1 1), (2 2, 4 4))

SELECT st_astext(st_multi(st_geomfromText('multipolygon(((0 0, 1 1, 2 0, 0 0)), ((10 0, 11 1, 12 0, 10 0)))', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
MULTIPOLYGON (((0 0, 1 1, 2 0, 0 0)), ((10 0, 11 1, 12 0, 10 0)))

SELECT st_astext(st_multi(st_geomfromText('geometrycollection(point(0 0), point(1 1))', 4326)), 0) multi FROM dual;

MULTI
----------------------------------------------------------------
GEOMETRYCOLLECTION (POINT (0 0), POINT (1 1))
```
