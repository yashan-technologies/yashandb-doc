```ebnf+diagram
st_setsrid::= ST_SETSRID "(" geometry "," srid ")"
```

ST_SETSRID函数用于将输入的geometry的空间参考系标识号（SRID）设置为指定的srid。

该函数根据输入的geometry返回设置好SRID的geometry。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**srid**

srid表示要设置的SRID，其值为INT类型。

* 输入的srid如果是负数，则按照0处理。

* 支持能够隐式转换成INT的类型，如果输入的是小数则进行四舍五入转换。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_AsText(ST_SetSrid(ST_GeomFromText('POINT(-58.2687 29.149)',1356), 4326)) res FROM DUAL;

RES
----------------------------------------------------------------
POINT (-58.268700000000003 29.149000000000001)

--ST_GEOMFROMWKB函数会根据给定的WKB和SRID返回一个ST_GEOMETRY数据
SELECT ST_SRID(ST_SetSrid(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040',4322), 4326)) res FROM DUAL;

        RES
------------ 
        4326

--参数包含NULL
SELECT ST_SRID(ST_SetSrid(ST_GeomFromText(null,1356), 4326)) res FROM DUAL;

         RES 
------------ 
            
```
