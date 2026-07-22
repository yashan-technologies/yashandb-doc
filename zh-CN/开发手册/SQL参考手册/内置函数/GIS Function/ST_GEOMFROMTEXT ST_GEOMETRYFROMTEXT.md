```ebnf
st_geomfromtext = ST_GEOMFROMTEXT "(" wkt [ "," srid ] ")".
```

ST_GEOMFROMTEXT（别名ST_GEOMETRYFROMTEXT）函数根据给定的wkt（Well-Known Text）和srid返回一个ST_GEOMETRY类型数据。

**wkt**

wkt的数据类型是CLOB，遵循如下规则：

*   wkt需要是一个有效的Well-Known Text，否则报错。
*   支持的坐标的最高维度是三维，如果输入是四维的坐标，或带有'M'字样的坐标，则会忽略第四个坐标轴，生成三维坐标。
*   如果输入的坐标中既有二维的点，也有三维的点，则会把二维的点提升到三维，Z轴的数值补零。
*   支持输入'inf'、'nan'，如果一个坐标全是'nan'，则会存为EMPTY。
*   支持输入空的ST_GEOMETRY数据（如POINT EMPTY）。
*   点坐标不能跟EMPTY同时使用，如MULTIPOINT(1 1，EMPTY)就会报错。
*   输入的wkt如果前面的字符已经构成了一个有效的ST_GEOMETRY数据，并且后面还有其他字符，则会生成前面的有效ST_GEOMETRY数据。
*   支持能够隐式转换成CLOB的数据类型。

**srid**

srid的数据类型是INT，表示该ST_GEOMETRY类型数据的空间参考系，遵循如下规则：

*   支持能够隐式转换成INT的类型，如果输入的是小数则进行四舍五入转换。
*   该参数可以省略，省略时默认值是0（srid=0表示没有定义空间参考系）。
*   如果输入的是负数，则会按照0进行处理。

当输入的参数存在NULL时，函数返回NULL，空串作为NULL处理。

示例（HEAP表）

```sql
--生成Point
SELECT ST_AsText(ST_GeomFromText('POINT(1 2)', 4326), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1 2)                                                     

--维度混合
SELECT ST_AsText(ST_GeomFromText('MULTIPOINT(1 2 3, 1 1)', 4326), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
MULTIPOINT Z (1 2 3, 1 1 0)                                     

--支持inf和nan的输入
SELECT ST_AsText(St_GeomFromText('MULTIPOINT(inf inf,nan nan)')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
MULTIPOINT (inf inf, EMPTY)                                     

--Srid
SELECT ST_Srid(st_GeomFromText('POINT(1 2)', 4326)) res FROM DUAL;

         RES 
------------ 
        4326

SELECT ST_Srid(st_GeomFromText('POINT(1 2)')) res FROM DUAL;

         RES 
------------ 
           0

SELECT ST_Srid(st_GeomFromText('POINT(1 2)', -12)) res FROM DUAL;

         RES 
------------ 
           0

--同名函数ST_GeometryFromText
SELECT ST_AsText(ST_GeometryFromText('POINT(1 2)', 4326), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (1 2)
```
