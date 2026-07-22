```ebnf
st_overlaps = ST_OVERLAPS "(" geometry1 "," geometry2 ")".
```

ST_OVERLAPS函数的功能是判断两个Geometry相交并具有相同的维度，但彼此之间不完全包含。如果geometry1和geometry2“空间重叠”则返回TRUE，否则返回FALSE。

如果两个Geometry具有相同的维度，每个Geometry至少有一个点不属于另一个Geometry（或等价地说，它们都不覆盖另一个Geometry），并且它们内部的交叉点具有相同的尺寸，那么它们就是重叠的。重叠关系是对称的。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

本函数遵守如下规则：

* 当输入的参数存在NULL时，函数返回NULL。
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。
* 能够保证的精度是小数点后面15位，小数部分超出15位之后结果不保证。
* 遵循DE-9IM（Dimensionally Extended 9-Intersection Model）规则。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_Overlaps(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 3)')) res FROM DUAL;

RES 
-------------------- 
true                

SELECT ST_Overlaps(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 5)')) res FROM DUAL;

RES
-------------------- 
false               

SELECT ST_Overlaps(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES
-------------------- 
                    
```
