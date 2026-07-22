```ebnf
st_srid = ST_SRID "(" geometry ")".
```

ST_SRID函数用于查询输入的geometry的空间参考系标识号（SRID）。

该函数会根据输入的geometry返回一个integer类型的SRID。

**geometry**

geometry是一个合法的ST_GEOMETRY类型的数据。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_SRID(ST_GeomFromText('POINT(-58.2687 29.149)',1356)) res FROM DUAL;

         RES 
------------ 
        1356

--ST_GEOMFROMWKB函数会根据给定的WKB和SRID返回一个ST_GEOMETRY数据
SELECT ST_SRID(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040',4322)) res FROM DUAL;

         RES 
------------ 
        4322

--参数包含NULL
SELECT ST_SRID(ST_GeomFromText(null,1356)) res from DUAL;

         RES 
------------ 
            
```
