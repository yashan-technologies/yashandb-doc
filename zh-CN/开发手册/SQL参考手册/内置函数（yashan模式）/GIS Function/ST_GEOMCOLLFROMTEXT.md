```ebnf+diagram
st_geomcollfromtext::= ST_GEOMCOLLFROMTEXT "(" wkt [ "," srid ] ")"
```

ST_GEOMCOLLFROMTEXT函数根据给定的wkt（Well-Known Text）和srid返回一个ST_GEOMETRY类型数据，如果传入的WKT不是GEOMETRYCOLLECTION，则返回NULL。

入参wkt和srid的规格与[ST_GEOMFROMTEXT](ST_GEOMFROMTEXT ST_GEOMETRYFROMTEXT)函数相同。

当输入的参数存在NULL时，函数返回NULL，空串作为NULL处理。

示例（HEAP表）

```sql
SELECT ST_AsText(ST_GeomCollFromText('GEOMETRYCOLLECTION EMPTY')) res FROM DUAL;

RES
---------------------------------------------------------------- 
GEOMETRYCOLLECTION EMPTY                                        

SELECT ST_AsText(ST_GeomCollFromText('GEOMETRYCOLLECTION(POINT(1 2), LINESTRING(1 3, 4 1))'), 0) res FROM DUAL;

RES
---------------------------------------------------------------- 
GEOMETRYCOLLECTION (POINT (1 2), LINESTRING (1 3, 4 1))         

SELECT ST_AsText(ST_GeomCollFromText('LINESTRING(1 3, 4 1)'), 0) res FROM DUAL;

RES
---------------------------------------------------------------- 
```
