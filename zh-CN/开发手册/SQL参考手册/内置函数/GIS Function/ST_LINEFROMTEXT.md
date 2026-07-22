```ebnf
st_linefromtext = ST_LINEFROMTEXT "(" wkt [ "," srid ] ")".
```

ST_LINEFROMTEXT函数根据给定的wkt（Well-Known Text）和srid返回一个ST_GEOMETRY类型数据，如果传入的WKT不是LINESTRING，则返回NULL。

入参wkt和srid的规格与[ST_GEOMFROMTEXT](ST_GEOMFROMTEXT ST_GEOMETRYFROMTEXT)函数相同。

当输入的参数存在NULL时，函数返回NULL，空串作为NULL处理。

示例（HEAP表）

```sql
SELECT ST_AsText(ST_LineFromText('LINESTRING EMPTY')) res FROM DUAL;

RES
---------------------------------------------------------------- 
LINESTRING EMPTY                                                

SELECT ST_AsText(ST_LineFromText('LINESTRING(1 1, 3 1)'), 0) res FROM DUAL;

RES
---------------------------------------------------------------- 
LINESTRING (1 1, 3 1)                                           

SELECT ST_AsText(ST_LineFromText('POINT(3 1)'), 0) res FROM DUAL;

RES
---------------------------------------------------------------- 
```
