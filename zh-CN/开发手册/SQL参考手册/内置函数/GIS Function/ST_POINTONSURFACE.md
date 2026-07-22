```ebnf
st_pointonsurface = ST_PointOnSurface"(" geometry ")".
```

ST_POINTONSURFACE函数用于获取位于表面内部的点。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

示例（HEAP表）

```sql
select ST_AsText(ST_PointOnSurface(st_geomfromtext('MULTIPOINT((-15 -15), (5 5), EMPTY)')), 0) from dual;

ST_ASTEXT(ST_POINTONSURFACE(ST_GEOMFROMTEXT('MULTIPOINT((-15-15) 
---------------------------------------------------------------- 
POINT (-15 -15)
```
