```ebnf+diagram
st_geomfromwkb::= ST_GEOMFROMWKB "(" wkb [ "," srid ] ")"
```

ST_GEOMFROMWKB函数根据给定的wkb（Well-Known Binary）和srid返回一个ST_GEOMETRY类型数据。

**wkb**

wkb的数据类型是BLOB，遵循如下规则：

*   支持能够隐式转换成BLOB的类型。
*   wkb需要是一个有效的Well-Known Binary，否则报错。
*   输入的wkb如果前面的字符已经构成了一个有效的ST_GEOMETRY数据，并且后面还有其他有效字符，则只会生成前面的有效ST_GEOMETRY数据。

**srid**

srid的数据类型是INT，表示该ST_GEOMETRY数据的空间参考系，遵循如下描述：

*   支持能够隐式转换成INT的类型，如果输入的是小数则进行四舍五入转换。
*   该参数可以省略，省略时默认值是0（srid=0表示没有定义空间参考系）。
*   如果输入的是负数，则输出原有SRID。

当输入的参数存在NULL时，函数返回NULL，空串作为NULL处理。

示例（HEAP表）

```sql
--生成Point
SELECT ST_AsText(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1.000000000000000 2.000000000000000)                     
                                                                          
--Srid
SELECT ST_Srid(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040', 4322)) res FROM DUAL;

         RES 
------------ 
        4322

SELECT ST_Srid(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040')) res FROM DUAL;

         RES 
------------ 
           0

SELECT ST_Srid(ST_GeomFromWkb('0101000000000000000000F03F0000000000000040', -10)) res FROM DUAL;

         RES 
------------ 
           0                                        
```
