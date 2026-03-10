```ebnf+diagram
st_makeline::= ST_MAKELINE "(" geometry1 "," geometry2  ")"
```

ST_MAKELINE函数根据输入的geometry1和geometry2，返回由组成它们的点按顺序连接的LINESTRING数据。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，遵循如下规则：

- 输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。
- geometry1和geometry2须为POINT类型、LINESTRING类型和MULTIPOINT类型的其中一种。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_AsText(ST_MakeLine(ST_GeomFromText('POINT(1 1)'), ST_GeomFromText('POINT(2 2)')), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
LINESTRING (1 1, 2 2)

SELECT ST_AsText(ST_MakeLine(ST_GeomFromText('LINESTRING(1 1, 2 2)'), ST_GeomFromText('POINT(3 3)')), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
LINESTRING (1 1, 2 2, 3 3)

SELECT ST_AsText(ST_MakeLine(ST_GeomFromText('MULTIPOINT(1 1 1, 2 2 2)'), ST_GeomFromText('POINT(3 3)')), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
LINESTRING Z (1 1 1, 2 2 2, 3 3 0)

--geometry1和geometry2的SRID不同时返回错误
SELECT ST_AsText(ST_MakeLine(ST_GeomFromText('MULTIPOINT(1 1 1, 2 2 2)',3), ST_GeomFromText('POINT(3 3)')), 0) res FROM DUAL;

YAS-07202 plugin execution error, different srid in st_makeline
```
