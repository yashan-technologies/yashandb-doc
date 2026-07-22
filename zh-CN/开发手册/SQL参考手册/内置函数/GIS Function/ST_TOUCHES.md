```ebnf
st_touches = ST_TOUCHES "(" geometry1 "," geometry2 ")".
```

ST_TOUCHES函数的功能是判断两个Geometry是否至少有一个共同点，且它们的内部不相交。如geometry1和geometry2相交，且它们的内部不相交，则返回TRUE，否则返回FALSE。

对于POINT/POINT输入的情况，总是返回FALSE，因为POINT没有边界。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

本函数遵守如下规则：

* 当输入的参数存在NULL时，函数返回NULL。
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。
* 能够保证的精度是小数点后面15位，小数部分超出15位之后结果不保证。
* 遵循DE-9IM（Dimensionally Extended 9-Intersection Model）规则。

如果两个Geometry的DE-9IM交叉矩阵匹配以下情况，则此关系成立：

* FT*******
* F\*\*T*****
* F\*\*\*T****

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_Touches(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 5)')) res FROM DUAL;

RES
-------------------- 
true                

SELECT ST_Touches(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 1 2, 3 5)')) res FROM DUAL;

RES
-------------------- 
false               

SELECT ST_Touches(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES
-------------------- 
                    
```
