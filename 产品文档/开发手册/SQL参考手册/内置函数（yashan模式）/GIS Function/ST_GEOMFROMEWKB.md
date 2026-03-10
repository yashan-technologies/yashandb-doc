```ebnf+diagram
st_geomfromewkb::= ST_GEOMFROMEWKB "(" ewkb ")"
```

ST_GEOMFROMEWKB函数根据给定的ewkb（Extended Well-Known Binary）返回一个ST_GEOMETRY类型的数据。

**ewkb**

ewkb的数据类型是BLOB，遵循如下规则：
*   支持能够隐式转换成BLOB的类型。
*   ewkb需要是一个合法的Extended Well-Known Binary，否则报错。
*   输入的ewkb如果前面的字符已经构成了一个有效的ST_GEOMETRY类型数据，并且后面还有其他有效字符，则只会生成前面的有效的ST_GEOMETRY类型数据。

当输入的ewkb为NULL时，函数返回NULL，空串作为NULL处理。

示例（HEAP表）

```sql
SELECT ST_AsText(ST_GeomFromEwkb('0101000020E6100000000000000000F03F0000000000000040')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1.000000000000000 2.000000000000000)                     

SELECT ST_Srid(ST_GeomFromEwkb('0101000020E6100000000000000000F03F0000000000000040')) res FROM DUAL;

         RES 
------------ 
        4326                                   
```
