```ebnf
st_pointz = ST_POINTZ "(" x "," y "," z [ "," srid ] ")".
```

ST_POINTZ函数根据输入的x、y、z和可选的srid，返回对应坐标和srid的POINT数据。

**x, y, z**

表示坐标，参数类型为数值型，遵循如下规则：

- 支持可以转换为DOUBLE类型的CHAR、VARCHAR类型（转换失败返回Invalid Number错误），使用其他数据类型则返回错误。
- 本函数仅支持三维坐标，使用其他维度的坐标则返回错误。

**srid**

srid的数据类型是INT，表示输出结果中的空间参考系，遵循如下规则：

*   支持能够隐式转换成INT的类型，如输入小数则进行四舍五入转换。
*   该参数可以省略，省略时默认值是0。
*   如果输入的是负数，则会按照默认的srid输出。

当输入的参数存在NULL时，函数返回NULL。

如需获取二维坐标的POINT数据，请使用[ST_POINT](ST_POINT)函数。

示例（HEAP表）

```sql
SELECT ST_AsText(ST_PointZ(1, 2, 3), 0) res FROM DUAL;

RES                                          
---------------------------------------------------------------- 
POINT Z (1 2 3)        

SELECT ST_AsText(ST_PointZ(1, 2, 3, 4326), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POINT Z (1 2 3)  

--SRID为负数时按照默认的SRID输出
SELECT ST_SRID(ST_PointZ(1, 2, 3, -8)) res FROM DUAL;

                 RES
-------------------- 
                   0                                            

SELECT ST_SRID(ST_PointZ(1, 2, 3)) res FROM DUAL;

                 RES 
-------------------- 
                   0

SELECT ST_SRID(ST_PointZ(1, 2, 3, 4326)) res FROM DUAL;

                 RES 
-------------------- 
                4326
```
